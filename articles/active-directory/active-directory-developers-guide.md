---
title: Guia para programadores do Azure Active Directory | Microsoft Docs
description: Este artigo fornece um guia completo aos recursos dirigidos aos programadores do Azure Active Directory.
services: active-directory
documentationcenter: dev-center-name
author: msmbaldwin
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2016
ms.author: mbaldwin

---
# Guia para programadores do Azure Active Directory
## Descrição geral
Como gestão de identidade como uma plataforma de serviço (IDMaaS), o Azure Active Directory (AD) fornece aos programadores uma forma eficaz de integrar a gestão de identidades nas suas aplicações. Os artigos que se seguem fornecem descrições gerais sobre a implementação e as principais funcionalidades do Azure AD. Sugerimos a sua leitura por ordem, ou avance para [Introdução](#getting-started) se já estiver pronto a aprofundar ainda mais o assunto.

1. [Os benefícios da integração do Azure AD](active-directory-how-to-integrate.md): saiba por que é que a integração com o Azure AD oferece a melhor solução para início de sessão e autorização seguros.
2. [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md): tire partido da autenticação simplificada do Azure AD para fornecer início de sessão na sua aplicação.
3. [Integrar aplicações com o Azure AD](active-directory-integrating-applications.md): saiba como adicionar, atualizar e remover aplicações do Azure AD e conheça as diretrizes de imagem institucional para aplicações integradas.
4. [Graph API do Azure AD](active-directory-graph-api.md): utilize a Graph API do Azure AD para aceder, programaticamente, ao Azure AD através de pontos finais da API REST. A Graph API do Azure AD também pode ser acedida através do [Microsoft Graph](https://graph.microsoft.io/). O Microsoft Graph proporciona uma API unificada que permite acesso a várias APIs do serviço em nuvem da Microsoft, através de um único ponto final de API REST, e com apenas um token de acesso.
5. [Bibliotecas de autenticação do Azure AD](active-directory-authentication-libraries.md): autentique facilmente os utilizadores para obter os tokens de acesso mediante a utilização de bibliotecas de autenticação do Azure AD para .NET, JavaScript, Objective-C, Android e outras.

## Introdução
Estes tutoriais são adaptados para várias plataformas e podem ajudá-lo a começar rapidamente o desenvolvimento com o Azure Active Directory. Como pré-requisito, tem de [obter um inquilino do Azure Active Directory](active-directory-howto-tenant.md).

### Guias de introdução a aplicações móveis e para PC
| [![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md) | [![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md) | [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md) | [![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md) | [![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md) | [![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md) | [![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md) |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| [iOS](active-directory-devquickstarts-ios.md) |[Android](active-directory-devquickstarts-android.md) |[.NET](active-directory-devquickstarts-dotnet.md) |[Windows Universal](active-directory-devquickstarts-windowsstore.md) |[Xamarin](active-directory-devquickstarts-xamarin.md) |[Cordova](active-directory-devquickstarts-cordova.md) |[Integrar diretamente com OAuth 2.0](active-directory-protocols-oauth-code.md) |

### Guias de introdução a Web Apps
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md) | [![JAva](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md) | [![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md) | [![Javascript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md) |
|:---:|:---:|:---:|:---:|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapp-dotnet.md) |[Java](active-directory-devquickstarts-webapp-java.md) |[AngularJS](active-directory-devquickstarts-angular.md) |[Javascript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |[Integrar diretamente com OpenID Connect](active-directory-protocols-openid-connect-code.md) |

### Guias de introdução à API Web
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md) |
|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapi-dotnet.md) |[Node.js](active-directory-devquickstarts-webapi-nodejs.md) |

### Consulte o guia de introdução ao diretório
| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md) |
|:---:|
| [Graph API](active-directory-graph-api-quickstart.md) |

## Procedimentos
Estes artigos descrevem como efetuar tarefas específicas com o Azure Active Directory:

* [Obter um inquilino do Azure AD](active-directory-howto-tenant.md)
* [Iniciar sessão de qualquer utilizador do Azure AD utilizando o padrão de aplicação multi-inquilino](active-directory-devhowto-multi-tenant-overview.md) 
* Ativar a SSO em várias aplicações com ADAL em dispositivos [Android](active-directory-sso-android.md) e [iOS](active-directory-sso-ios.md)
* [Tornar a sua aplicação AppSource certificada para Azure AD](active-directory-devhowto-appsource-certified.md)
* [Liste a sua aplicação na galeria de aplicações do Azure AD](active-directory-app-gallery-listing.md)
* [Submeter Web Apps do Office 365 ao Dashboard do Vendedor](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
* [Compreender o manifesto da aplicação do Azure Active Directory](active-directory-application-manifest.md)
* [Compreender as diretrizes de imagem corporativa para os botões de início de sessão e aquisição de aplicações na aplicação de cliente](active-directory-branding-guidelines.md)
* [Pré-visualização: como criar aplicações que inscrevem os utilizadores em contas pessoais, profissionais ou escolares](active-directory-appmodel-v2-overview.md)
* [Pré-visualização: como criar aplicações que inscrevem e iniciam sessão dos consumidores](../active-directory-b2c/active-directory-b2c-overview.md)

## Referência
Estes artigos fornecem uma referência de base para as APIs REST e da biblioteca de autenticação, protocolos, erros, exemplos de código e pontos finais.  

### Suporte
* [Perguntas com etiquetas](http://stackoverflow.com/questions/tagged/azure-active-directory): localize soluções do Azure Active Directory no Stack Overflow procurando as etiquetas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) e [adal](http://stackoverflow.com/questions/tagged/adal).
* Veja o [Glossário de programador do Azure AD](active-directory-dev-glossary.md) para obter definições de alguns dos termos utilizados frequentemente relacionados com a programação e integração de aplicações.

### Código
* [Bibliotecas open source do Azure Active Directory](http://github.com/AzureAD): a forma mais fácil de localizar a origem de uma biblioteca utilizando a nossa [lista de bibliotecas](active-directory-authentication-libraries.md).
* [Exemplos do Azure Active Directory](https://github.com/azure-samples?query=active-directory): a forma mais fácil de navegar na lista de exemplos é utilizando o [índice dos exemplos de código](active-directory-code-samples.md).
* [ADAL para .NET](https://msdn.microsoft.com/library/azure/mt417579.aspx): documentação para a biblioteca de autenticação do .NET.

### Graph API
* [Referência da Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx): referência REST para a Graph API do Azure Active Directory. [Veja a experiência referência interativa da Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* [Âmbitos de permissões da Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): âmbitos de permissões de OAuth 2.0 utilizados para controlar o acesso que uma aplicação tem aos dados do diretório de um inquilino.

### Protocolos de autenticação e autorização
* [Assinar Rollover de chave no Azure AD](active-directory-signing-key-rollover.md): saiba mais sobre a cadência da chave de assinatura do Azure AD e como atualizar a chave para os cenários mais comuns de aplicação.
* [Protocolo OAuth 2.0: utilizar a concessão do código de autorização](active-directory-protocols-oauth-code.md): pode utilizar a concessão do código de autorização do protocolo OAuth 2.0 para autorizar o acesso às aplicações Web e APIs Web no inquilino do Azure Active Directory.
* [Protocolo OAuth 2.0: noções sobre a concessão implícita](active-directory-dev-understanding-oauth2-implicit-grant.md): saiba mais sobre a concessão de autorização implícita e se é adequada para a sua aplicação.
* [Protocolo OAuth 2.0: chamadas de serviço para serviço com a utilização de credenciais de cliente ](active-directory-protocols-oauth-service-to-service.md): a concessão de Credenciais de Cliente de OAuth 2.0 permite que um serviço Web (um cliente confidencial) utilize as suas próprias credenciais para a autenticação ao chamar outro serviço Web, em vez de representar um utilizador. Neste cenário, o cliente é normalmente um serviço Web de camada média, um serviço do daemon ou um site.
* [Protocolo OpenID Connect 1.0: início de sessão e autenticação](active-directory-protocols-openid-connect-code.md): o protocolo OpenID Connect 1.0 expande o OAuth 2.0 para ser utilizado como protocolo de autenticação. Uma aplicação de cliente pode receber uma id_token para gerir o processo de início de sessão ou aumentar o fluxo de código de autorização para receber uma id_token e o código de autorização.
* [Referência do protocolo SAML 2.0](active-directory-saml-protocol-reference.md): o protocolo SAML 2.0 permite às aplicações fornecer uma experiência de início de sessão única aos respetivos utilizadores.
* [Protocolo WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): o Azure Active Directory suporta o WS-Federation 1.2 de acordo com a Especificação Versão 1.2 do Web Services Federation. Para obter mais informações sobre o documento de metadados de federação, veja o artigo [Metadados de Federação](active-directory-federation-metadata.md).
* [Token e tipos de afirmações suportados](active-directory-token-and-claims.md): pode utilizar este guia para compreender e avaliar as afirmações no SAML 2.0 e JSON Web Tokens (JWT).

## Vídeos
### Compilação
Estas apresentações gerais sobre o desenvolvimento de aplicações utilizando o Azure Active Directory são de oradores que trabalham diretamente na equipa de engenharia. As apresentações abrangem tópicos fundamentais, incluindo IDMaaS, autenticação, federação de identidades e início de sessão único.

* [Microsoft Identity: Estado da União e Direção Futura](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
* [Azure Active Directory: gestão de identidades como um serviço para aplicações modernas](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
* [Desenvolver Web Apps modernas com o Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
* [Desenvolver aplicações nativas modernas com o Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### Azure Friday
O [Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) faz parte de uma série de vídeos Friday 1:1 dedicados a facultar-lhe entrevistas curtas (10-15 minutos) com peritos em diversos tópicos do Azure.  Utilize a funcionalidade Filtrar Serviços na página para ver todos os vídeos do Azure Active Directory.

* [Identidade do Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
* [Identidade do Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
* [Identidade do Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Redes sociais
* [Blogue da Equipa do Active Directory](http://blogs.technet.com/b/ad/): os desenvolvimentos mais recentes do mundo do Azure Active Directory.
* [Blogue da Equipa do Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam): informações sobre o Azure Active Directory, específicas para a Graph API.
* [Identidade na Nuvem](http://www.cloudidentity.net): opiniões sobre a gestão de identidades como um serviço, a partir de um PM do Azure Active Directory.  
* [Azure Active Directory no Twitter](https://twitter.com/azuread): anúncios do Azure Active Directory com 140 caracteres ou menos.

<!--HONumber=Sep16_HO3-->


