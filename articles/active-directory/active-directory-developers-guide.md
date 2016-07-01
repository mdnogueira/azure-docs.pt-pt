<properties
   pageTitle="Guia para programadores do Azure Active Directory | Microsoft Azure"
   description="Este artigo fornece um guia completo aos recursos dirigidos aos programadores do Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/02/2016"
   ms.author="mbaldwin"/>


# Guia para programadores do Azure Active Directory

## Descrição geral
Como gestão de identidade como uma plataforma de serviço (IDMaaS), o Azure Active Directory fornece aos programadores uma forma eficaz de integrar a gestão de identidades às suas aplicações. Os artigos que se seguem fornecem descrições gerais sobre a implementação e as principais funcionalidades do Azure Active Directory. Sugerimos a sua leitura por ordem, ou avance para [Introdução](#getting-started) se já estiver pronto a aprofundar ainda mais o assunto.


1. [As vantagens da integração do Azure Active Directory](active-directory-how-to-integrate.md): detetar por que motivo a integração com o Azure Active Directory oferece a melhor solução para o início de sessão seguro e autorização.

1. [Cenários de autenticação do Active Directory](active-directory-authentication-scenarios.md): tirar partido da autenticação simplificada do Azure Active Directory para fornecer início de sessão na aplicação.

1. [Integrar aplicações com o Azure Active Directory](active-directory-integrating-applications.md): saiba como adicionar, atualizar e remover aplicações a partir do Azure Active Directory e sobre as diretrizes de imagem corporativa para aplicações integradas.

1. [Graph API do Azure Active Directory](active-directory-graph-api.md): utilize a Graph API do Azure Active Directory para aceder, através de programação, ao Azure Active Directory através de pontos finais da API REST. Tenha em conta que a Graph API do Azure AD também é acessível através do [Microsoft Graph](https://graph.microsoft.io/), uma API unificada que permite aceder a várias APIs de serviço em nuvem da Microsoft, através de um único ponto final de API REST e com um único token de acesso.

1. [Bibliotecas de autenticação do Azure Active Directory](active-directory-authentication-libraries.md): autentique facilmente os utilizadores para obter os tokens de acesso através do recurso a bibliotecas de autenticação do Azure AD para .NET, JavaScript, Objective-C, Android e muito mais.


## Introdução

Estes tutoriais são adaptados para várias plataformas e podem ajudá-lo a começar rapidamente o desenvolvimento com o Azure Active Directory. Como pré-requisito, tem de [obter um inquilino do Azure Active Directory](active-directory-howto-tenant.md).

### Guias de introdução a aplicações móveis e para PC

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)|[![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Loja Windows](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)

### Guias de introdução a Web Apps

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![JAva](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![Javascript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md)
|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[Javascript](active-directory-devquickstarts-angular.md)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)

### Guias de introdução à API Web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### Consulte o guia de introdução ao diretório

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[Graph API](active-directory-graph-api-quickstart.md)|

## Procedimentos

Estes artigos descrevem como efetuar tarefas específicas com o Azure Active Directory:

- [Obter um inquilino do Azure Active Directory](active-directory-howto-tenant.md)
- [Listar a aplicação na galeria de aplicações do Azure Active Directory](active-directory-app-gallery-listing.md)
- [Compreender o manifesto da aplicação do Azure Active Directory](active-directory-application-manifest.md)
- [Criar uma aplicação com APIs do Office 365](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Submeter Web Apps do Office 365 ao Dashboard do Vendedor](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- Saiba como ativar a SSO em vária aplicações com ADAL em dispositivos [Android](active-directory-sso-android.md) e [iOS](active-directory-sso-ios.md)
- [Pré-visualização: como criar aplicações que inscrevem os utilizadores em contas pessoais, profissionais ou escolares](active-directory-appmodel-v2-overview.md)
- [Pré-visualização: como criar aplicações que inscrevem e iniciam sessão dos consumidores](../active-directory-b2c/active-directory-b2c-overview.md)


## Referência

Estes artigos fornecem uma referência de base para as APIs REST e da biblioteca de autenticação, protocolos, erros, exemplos de código e pontos finais.  

###  Suporte
- [Perguntas com etiquetas](http://stackoverflow.com/questions/tagged/azure-active-directory): localize soluções do Azure Active Directory no Stack Overflow procurando as etiquetas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) e [adal](http://stackoverflow.com/questions/tagged/adal).

### Código

- [Bibliotecas open source do Azure Active Directory](http://github.com/AzureAD): a forma mais fácil de localizar a origem de uma biblioteca utilizando a nossa [lista de bibliotecas](active-directory-authentication-libraries.md).

- [Exemplos do Azure Active Directory](https://github.com/azure-samples?query=active-directory): a forma mais fácil de navegar na lista de exemplos é utilizando o [índice dos exemplos de código](active-directory-code-samples.md).

- [ADAL para .NET](https://msdn.microsoft.com/library/azure/mt417579.aspx): documentação para a biblioteca de autenticação do .NET.

### Graph API

- [Referência da Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx): referência REST para a Graph API do Azure Active Directory. [Veja a experiência referência interativa da Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Âmbitos de permissões da Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): âmbitos de permissões de OAuth 2.0 utilizados para controlar o acesso que uma aplicação tem aos dados do diretório de um inquilino.

### Protocolos de autenticação

- [Protocolos de autenticação do Azure Active Directory](active-directory-protocols.md): saiba mais acerca dos diferentes protocolos de autenticação e autorização suportados pelo Azure Active Directory.

- [Referência do protocolo SAML 2.0](active-directory-saml-protocol-reference.md): o protocolo SAML 2.0 permite às aplicações fornecer uma experiência de início de sessão única aos respetivos utilizadores.

- [Referência do protocolo OAuth 2.0](active-directory-protocols-oauth-code.md): pode utilizar o protocolo OAuth 2.0 para autorizar o acesso às Web Apps e APIs Web no inquilino do Azure Active Directory.

- [Referência do protocolo OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md): o protocolo OpenID Connect 1.0 expande o OAuth 2.0 para ser utilizado como protocolo de autenticação.

- [Protocolo WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): o Azure Active Directory suporta o WS-Federation 1.2 de acordo com a Especificação Versão 1.2 do Web Services Federation.

- [Token e tipos de afirmações suportados](active-directory-token-and-claims.md): pode utilizar este guia para compreender e avaliar as afirmações no SAML 2.0 e JSON Web Tokens (JWT).

## Vídeos

### Compilação

Estas apresentações gerais sobre o desenvolvimento de aplicações utilizando o Azure Active Directory são de oradores que trabalham diretamente na equipa de engenharia. As apresentações abrangem tópicos fundamentais, incluindo IDMaaS, autenticação, federação de identidades e início de sessão único.

- [Microsoft Identity: Estado da União e Direção Futura](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
- [Azure Active Directory: gestão de identidades como um serviço para aplicações modernas](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Desenvolver Web Apps modernas com o Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Desenvolver aplicações nativas modernas com o Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### Azure Friday
O [Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) faz parte de uma série de vídeos Friday 1:1 dedicados a facultar-lhe entrevistas curtas (10-15 minutos) com peritos em diversos tópicos do Azure.  Utilize a funcionalidade Filtrar Serviços na página para ver todos os vídeos do Azure Active Directory.

- [Identidade do Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Identidade do Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Identidade do Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Redes sociais

- [Blogue da Equipa do Active Directory](http://blogs.technet.com/b/ad/): os desenvolvimentos mais recentes do mundo do Azure Active Directory.

- [Blogue da Equipa do Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam): informações sobre o Azure Active Directory, específicas para a Graph API.

- [Identidade na Nuvem](http://www.cloudidentity.net): opiniões sobre a gestão de identidades como um serviço, a partir de um PM do Azure Active Directory.  

- [Azure Active Directory no Twitter](https://twitter.com/azuread): anúncios do Azure Active Directory com 140 caracteres ou menos.



<!--HONumber=Jun16_HO2-->


