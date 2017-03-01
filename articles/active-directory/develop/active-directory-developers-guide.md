---
title: Guia para programadores do Azure Active Directory | Microsoft Docs
description: Este artigo fornece um guia completo aos recursos dirigidos aos programadores do Azure Active Directory.
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/09/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 8da480acc04922a3636821c5303f6380236c5ea3
ms.openlocfilehash: 1bff76371ea1ca3ee68c9f04ea24a42f95bb7da7


---
# <a name="azure-active-directory-developers-guide"></a>Guia para programadores do Azure Active Directory
## <a name="overview"></a>Descrição geral
Como gestão de identidade como uma plataforma de serviço (IDMaaS), o Azure Active Directory (AD) fornece aos programadores uma forma eficaz de integrar a gestão de identidades nas suas aplicações. Os artigos que se seguem fornecem descrições gerais sobre a implementação e as principais funcionalidades do Azure AD. Sugerimos a sua leitura por ordem, ou avance para [Introdução](#getting-started) se já estiver pronto a aprofundar ainda mais o assunto.

1. [Os benefícios da integração do Azure AD](active-directory-how-to-integrate.md): saiba por que é que a integração com o Azure AD oferece a melhor solução para início de sessão e autorização seguros.
2. [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md): tire partido da autenticação simplificada do Azure AD para fornecer início de sessão na sua aplicação.
3. [Integrar aplicações com o Azure AD](active-directory-integrating-applications.md): saiba como adicionar, atualizar e remover aplicações do Azure AD e conheça as diretrizes de imagem institucional para aplicações integradas.
4. [Graph API do Azure AD](active-directory-graph-api.md): utilize a Graph API do Azure AD para aceder, programaticamente, ao Azure AD através de pontos finais da API REST. A Graph API do Azure AD também pode ser acedida através do [Microsoft Graph](https://graph.microsoft.io/). O Microsoft Graph proporciona uma API unificada que permite acesso a várias APIs do serviço em nuvem da Microsoft, através de um único ponto final de API REST, e com apenas um token de acesso.
5. [Bibliotecas de autenticação do Azure AD](active-directory-authentication-libraries.md): autentique facilmente os utilizadores para obter os tokens de acesso mediante a utilização de bibliotecas de autenticação do Azure AD para .NET, JavaScript, Objective-C, Android e outras.

## <a name="getting-started"></a>Introdução
Estes tutoriais são adaptados para várias plataformas e podem ajudá-lo a começar rapidamente o desenvolvimento com o Azure Active Directory. Como pré-requisito, tem de [obter um inquilino do Azure Active Directory](active-directory-howto-tenant.md).

### <a name="mobile-and-pc-application-quick-start-guides"></a>Guias de introdução a aplicações móveis e para PC
| [![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md) | [![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md) | [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md) | [![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md) | 
|:---:|:---:|:---:|:---:|:---:|
| [iOS](active-directory-devquickstarts-ios.md) |[Android](active-directory-devquickstarts-android.md) |[.NET](active-directory-devquickstarts-dotnet.md) |[Windows</br>Universal](active-directory-devquickstarts-windowsstore.md) |

|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md) | [![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md) | [![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md) |
|:---:|:---:|:---:|
|[Xamarin](active-directory-devquickstarts-xamarin.md) |[Cordova](active-directory-devquickstarts-cordova.md) |[Integrar diretamente</br>com OAuth 2.0](active-directory-protocols-oauth-code.md) |

### <a name="web-application-quick-start-guides"></a>Guias de introdução a Web Apps
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md) | [![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md) | [![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md) |
|:---:|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapp-dotnet.md) |[Java](active-directory-devquickstarts-webapp-java.md) |[AngularJS](active-directory-devquickstarts-angular.md) |[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |[Integrar diretamente</br> com OpenID Connect](active-directory-protocols-openid-connect-code.md) |

| [![Javascript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md) |
|:---:|:---:|:---:|
|[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |[Integrar diretamente</br> com OpenID Connect](active-directory-protocols-openid-connect-code.md) |

### <a name="web-api-quick-start-guides"></a>Guias de introdução à API Web
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md) |
|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapi-dotnet.md) |[Node.js](active-directory-devquickstarts-webapi-nodejs.md) |

### <a name="querying-the-directory-quickstart-guide"></a>Consulte o guia de introdução ao diretório
| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md) |
|:---:|
| [Graph API](active-directory-graph-api-quickstart.md) |

## <a name="how-tos"></a>Procedimentos
Estes artigos descrevem como efetuar tarefas específicas com o Azure Active Directory:

* [Obter um inquilino do Azure AD](active-directory-howto-tenant.md)
* [Iniciar sessão de qualquer utilizador do Azure AD utilizando o padrão de aplicação multi-inquilino](active-directory-devhowto-multi-tenant-overview.md)
* [Utilizar um certificado em vez de um segredo para autenticar uma identidade de aplicação](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/)
* Ativar a SSO em várias aplicações com ADAL em dispositivos [Android](active-directory-sso-android.md) e [iOS](active-directory-sso-ios.md)
* [Tornar a sua aplicação AppSource certificada para Azure AD](active-directory-devhowto-appsource-certified.md)
* [Liste a sua aplicação na galeria de aplicações do Azure AD](active-directory-app-gallery-listing.md)
* [Submeter Web Apps do Office 365 ao Dashboard do Vendedor](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
* [Registar uma aplicação com o Azure Active Directory com o Portal do Azure](../active-directory-app-registration.md)
* [Compreender o manifesto da aplicação do Azure Active Directory](active-directory-application-manifest.md)
* [Compreender as diretrizes de imagem corporativa para os botões de início de sessão e aquisição de aplicações na aplicação de cliente](active-directory-branding-guidelines.md)
* [Pré-visualização: como criar aplicações que inscrevem os utilizadores em contas pessoais, profissionais ou escolares](active-directory-appmodel-v2-overview.md)
* [Pré-visualização: como criar aplicações que inscrevem e iniciam sessão dos consumidores](../../active-directory-b2c/active-directory-b2c-overview.md)
* [Pré-visualização: configurar durações do token no Azure AD](../active-directory-configurable-token-lifetimes.md) através do PowerShell. Consulte as [Operações de política](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) e a [Entidade de política](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) para obter detalhes sobre a configuração através da Graph API do Azure AD.

## <a name="reference"></a>Referência
Estes artigos fornecem uma referência de base para as APIs REST e da biblioteca de autenticação, protocolos, erros, exemplos de código e pontos finais.  

### <a name="support"></a>Suporte
* [Perguntas com etiquetas](http://stackoverflow.com/questions/tagged/azure-active-directory): localize soluções do Azure Active Directory no Stack Overflow procurando as etiquetas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) e [adal](http://stackoverflow.com/questions/tagged/adal).
* Veja o [Glossário de programador do Azure AD](active-directory-dev-glossary.md) para obter definições de alguns dos termos utilizados frequentemente relacionados com a programação e integração de aplicações.

### <a name="code"></a>Código
* [Bibliotecas open source do Azure Active Directory](http://github.com/AzureAD): a forma mais fácil de localizar a origem de uma biblioteca utilizando a nossa [lista de bibliotecas](active-directory-authentication-libraries.md).
* [Exemplos do Azure Active Directory](https://github.com/azure-samples?query=active-directory): a forma mais fácil de navegar na lista de exemplos é utilizando o [índice dos exemplos de código](active-directory-code-samples.md).
* [Biblioteca de Autenticação do Active Directory (ADAL) para .NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) - A documentação de referência está disponível para [a versão principal mais recente](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) e [a versão principal anterior](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory).

### <a name="graph-api"></a>Graph API
* [Referência da Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx): referência REST para a Graph API do Azure Active Directory. [Veja a experiência referência interativa da Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* [Âmbitos de permissões da Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): âmbitos de permissões de OAuth 2.0 utilizados para controlar o acesso que uma aplicação tem aos dados do diretório de um inquilino.

### <a name="authentication-and-authorization-protocols"></a>Protocolos de autenticação e autorização
* [Assinar Rollover de chave no Azure AD](active-directory-signing-key-rollover.md): saiba mais sobre a cadência da chave de assinatura do Azure AD e como atualizar a chave para os cenários mais comuns de aplicação.
* [Protocolo OAuth 2.0: utilizar a concessão do código de autorização](active-directory-protocols-oauth-code.md): pode utilizar a concessão do código de autorização do protocolo OAuth 2.0 para autorizar o acesso às aplicações Web e APIs Web no inquilino do Azure Active Directory.
* [Protocolo OAuth 2.0: noções sobre a concessão implícita](active-directory-dev-understanding-oauth2-implicit-grant.md): saiba mais sobre a concessão de autorização implícita e se é adequada para a sua aplicação.
* [Protocolo OAuth 2.0: chamadas de serviço para serviço com a utilização de credenciais de cliente ](active-directory-protocols-oauth-service-to-service.md): a concessão de Credenciais de Cliente de OAuth 2.0 permite que um serviço Web (um cliente confidencial) utilize as suas próprias credenciais para a autenticação ao chamar outro serviço Web, em vez de representar um utilizador. Neste cenário, o cliente é normalmente um serviço Web de camada média, um serviço do daemon ou um site.
* [Protocolo OpenID Connect 1.0: início de sessão e autenticação](active-directory-protocols-openid-connect-code.md): o protocolo OpenID Connect 1.0 expande o OAuth 2.0 para ser utilizado como protocolo de autenticação. Uma aplicação de cliente pode receber uma id_token para gerir o processo de início de sessão ou aumentar o fluxo de código de autorização para receber uma id_token e o código de autorização.
* [Referência do protocolo SAML 2.0](active-directory-saml-protocol-reference.md): o protocolo SAML 2.0 permite às aplicações fornecer uma experiência de início de sessão única aos respetivos utilizadores.
* [Protocolo WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): o Azure Active Directory suporta o WS-Federation 1.2 de acordo com a Especificação Versão 1.2 do Web Services Federation. Para obter mais informações sobre o documento de metadados de federação, veja o artigo [Metadados de Federação](active-directory-federation-metadata.md).
* [Token e tipos de afirmações suportados](active-directory-token-and-claims.md): pode utilizar este guia para compreender e avaliar as afirmações no SAML 2.0 e JSON Web Tokens (JWT).

## <a name="videos"></a>Vídeos
### <a name="build"></a>Compilação
Estas apresentações gerais sobre o desenvolvimento de aplicações utilizando o Azure Active Directory são de oradores que trabalham diretamente na equipa de engenharia. As apresentações abrangem tópicos fundamentais, incluindo IDMaaS, autenticação, federação de identidades e início de sessão único.

* [Microsoft Identity: Estado da União e Direção Futura](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
* [Azure Active Directory: gestão de identidades como um serviço para aplicações modernas](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
* [Desenvolver Web Apps modernas com o Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
* [Desenvolver aplicações nativas modernas com o Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure Friday
O [Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) faz parte de uma série de vídeos Friday 1:1 dedicados a facultar-lhe entrevistas curtas (10-15 minutos) com peritos em diversos tópicos do Azure.  Utilize a funcionalidade Filtrar Serviços na página para ver todos os vídeos do Azure Active Directory.

* [Identidade do Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
* [Identidade do Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
* [Identidade do Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>Redes sociais
* [Blogue da Equipa do Active Directory](http://blogs.technet.com/b/ad/): os desenvolvimentos mais recentes do mundo do Azure Active Directory.
* [Blogue da Equipa do Azure Active Directory Graph](https://blogs.msdn.microsoft.com/aadgraphteam): informações sobre o Azure Active Directory, específicas para a Graph API.
* [Identidade na Nuvem](http://www.cloudidentity.net): opiniões sobre a gestão de identidades como um serviço, a partir de um PM do Azure Active Directory.  
* [Azure Active Directory no Twitter](https://twitter.com/azuread): anúncios do Azure Active Directory com 140 caracteres ou menos.

## <a name="windows-server-on-premises-development"></a>Desenvolvimento do Windows Server no local
Para obter orientações sobre a utilização do Windows Server e o desenvolvimento dos Serviços de Federação do Active Directory (ADFS), veja:

* [Cenários do AD FS para Programadores](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers): proporciona uma descrição geral dos componentes do AD FS e como este funciona, com detalhes sobre os cenários suportados de autenticação/autorização.
* [Instruções do AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): uma lista de artigos de instruções, que proporciona instruções passo a passo para implementar os fluxos de autenticação/autorização relacionados.



<!--HONumber=Feb17_HO3-->


