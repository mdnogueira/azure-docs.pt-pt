---
title: "Bibliotecas de autenticação de v 2.0 do Azure Active Directory | Microsoft Docs"
description: "Bibliotecas de cliente compatível e bibliotecas de middleware de servidor e biblioteca relacionada, origem e ligações de exemplos, para o ponto de final de v 2.0 do Azure Active Directory."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/22/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 50a1cc0bf9e00cf5b866b88b3e88c62b06a2376b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Bibliotecas de autenticação de v 2.0 do Azure Active Directory
O [ponto final v 2.0 do Azure Active Directory (Azure AD)](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-compare) suporta os protocolos de OAuth 2.0 e o OpenID Connect 1.0 de norma da indústria. Pode utilizar bibliotecas vários da Microsoft e de outras organizações com o ponto final v 2.0.

Quando criar uma aplicação que utiliza o ponto final v 2.0, recomendamos a utilização de bibliotecas que são escritas por especialistas de domínio de protocolo que siga uma metodologia Security Development Lifecycle (SDL), como [o seguido pela Microsoft][Microsoft-SDL]. Se optar por suporte de código mão para os protocolos, recomendamos que siga metodologia SDL e preste especial atenção para as considerações de segurança nas especificações normas para cada protocolo.

> [!NOTE]
> Procurar as bibliotecas de v 1.0 do Azure AD (ADAL)? Finalizar a [guia da biblioteca ADAL](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-authentication-libraries). 
> 
> 

## <a name="types-of-libraries"></a>Tipos de bibliotecas
Ponto final v 2.0 do AD do Azure funciona com dois tipos de bibliotecas:

* **Bibliotecas de cliente**. Servidores e clientes nativos utilizam bibliotecas de cliente para obter os tokens de acesso para chamar um recurso, tal como o Microsoft Graph.
* **Bibliotecas de middleware servidor**. As Web apps utilizam bibliotecas de middleware de servidor para o início de sessão do utilizador. APIs da Web utilizam bibliotecas de middleware de servidor para validar os tokens que são enviados por clientes nativos ou por outros servidores.

## <a name="library-support"></a>Suporte de biblioteca
Uma vez que pode escolher qualquer biblioteca compatível com normas quando utiliza o ponto final v 2.0, é importante saber onde ir para o suporte. Para problemas e pedidos de funcionalidades no código da biblioteca, contacte o proprietário da biblioteca. Para problemas e pedidos de funcionalidades na implementação de protocolo do lado do serviço, contacte a Microsoft.

Bibliotecas são fornecidos em duas categorias de suporte:

* **Microsoft-suportado**. Microsoft disponibiliza correções para estas bibliotecas e tem feito SDL diligence devida nestas bibliotecas.
* **Compatível**. Microsoft foi testado estas bibliotecas em cenários básicos e ter confirmado que funcionam com o ponto final v 2.0. A Microsoft não cede corrige para estas bibliotecas e não tiver efetuado uma revisão destas bibliotecas. Problemas e pedidos de funcionalidades devem ser direcionados para o projeto de fonte aberta da biblioteca.

Para obter uma lista de bibliotecas que funcionam com o ponto final v 2.0, consulte as secções seguintes neste artigo.


## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de cliente suportados pelo Microsoft

> [!IMPORTANT]
> As bibliotecas de pré-visualização MSAL são adequadas para utilização num ambiente de produção. Podemos fornecer o mesmo suporte de nível de produção para estas bibliotecas, tal como estamos nossas bibliotecas de produção atual (ADAL). Durante a pré-visualização, iremos poder efetuar alterações para a API de MSAL, formato da cache interna e outros mecanismos destas bibliotecas sem aviso prévio, será necessário efetuar juntamente com correções de erros ou melhoramentos de funcionalidades. Podem ter impacto na sua aplicação. Por exemplo, uma alteração para o formato de cache pode afetar os seus utilizadores, por exemplo, exigindo que inicie sessão novamente. Uma alteração de API pode necessitar de atualizar o seu código. Quando a versão de disponibilidade geral, que podemos irá requerem que Atualize para a versão de disponibilidade geral dentro de seis meses que fornecemos, como aplicações escritas utilizando uma versão de pré-visualização versão da biblioteca pode deixará de funcionar.

| Plataforma | Biblioteca | Transferência | Código de origem | Exemplo | Referência
| --- | --- | --- | --- | --- | --- |
| Cliente de .NET, Loja Windows, UWP, Xamarin iOS e Android | MSAL .NET (pré-visualização) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplicação de ambiente de trabalho](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (pré-visualização) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Aplicação de página única](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS, macOS | MSAL (pré-visualização) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Aplicação iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL (pré-visualização) | [O repositório Central](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplicação Android](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliotecas de middleware suportada do Microsoft server

| Plataforma | Biblioteca | Transferência | Código de origem | Exemplo | Referência
| --- | --- | --- | --- | --- | --- |
| .NET 4. x | Middleware OWIN OpenID Connect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicação MVC](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4. x | Middleware de portador de OAuth da OWIN para AzureAD |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |  | |
| .NET 4. x | Processador JWT para o .NET 4.5 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | Middleware OpenID Connect do ASP.NET |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[Segurança do ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[Aplicação MVC](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | ASP.NET middleware de portador do OAuth |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[Segurança do ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | Processador JWT para .NET Core  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Passport do Azure AD |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Aplicação Web](active-directory-v2-devquickstarts-node-web.md)| |

## <a name="compatible-client-libraries"></a>Bibliotecas de cliente compatível
| Plataforma | Nome da biblioteca | Versão testada | Código de origem | Exemplo |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Exemplo de aplicação nativa](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Exemplo de aplicação nativa](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |

## <a name="compatible-server-middleware-libraries"></a>Bibliotecas de middleware servidor compatível
| Plataforma | Nome da biblioteca | Versão testada | Código de origem | Exemplo |
|:---:|:---:|:---:|:---:|:---:|
| Java | [Scribejava Scribe Java](https://github.com/scribejava/scribejava) | [Versão 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/archive/scribejava-3.2.0.zip) | |
| PHP | [O PHP League oauth2 cliente](https://github.com/thephpleague/oauth2-client) | [Versão 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [cliente de oauth2](https://github.com/thephpleague/oauth2-client/archive/1.4.2.zip) | |
| Python Flask |[Flask OAuthlib](https://github.com/lepture/flask-oauthlib) |0.9.3 |[Flask OAuthlib](https://github.com/lepture/flask-oauthlib) |[Aplicação Web](https://github.com/Azure-Samples/active-directory-python-flask-graphapi-web-v2) |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>Conteúdo relacionado
Para mais informações sobre o ponto final v 2.0 do Azure AD, consulte o [descrição geral v 2.0 do modelo de aplicação do Azure AD][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/
