---
title: "Exemplos de código do Azure Active Directory | Microsoft Docs"
description: "Um índice dos exemplos de código do Azure Active Directory, organizados por cenário."
services: active-directory
documentationcenter: dev-center-name
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2017
ms.author: mbaldwin
ms.custom: aaddev
ms.openlocfilehash: 19d71e65dc2c78663d94c74228dcff038566391b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-code-samples"></a>Exemplos de código do Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Pode utilizar o Microsoft Azure Active Directory (Azure AD) para adicionar a autenticação e autorização para as suas aplicações web e web APIs. Esta secção fornece ligações a exemplos que mostram como é efetuada e de fragmentos de código que pode utilizar nas suas aplicações. Na página de exemplo de código, encontrará detalhada Leia-me tópicos de ajudam com requisitos, instalação e configuração. E o código é comentado para o ajudar a compreender as secções críticas.

Para compreender o cenário básico para cada tipo de exemplo, consulte cenários de autenticação para o Azure AD.

Contribuir para os nossos exemplos em GitHub: [e documentação do Microsoft Azure Active Directory Samples](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="web-browser-to-web-application"></a>Browser para a aplicação Web
Estes exemplos mostram como escrever uma aplicação web que direciona o browser do utilizador para iniciar sessão-los para o Azure AD.

| Idioma/plataforma | Exemplo | Descrição |
| --- | --- | --- |
| C# / .NET |[WebApp-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) |Utilize o OpenID Connect (ASP.Net OpenID Connect OWIN middleware) para autenticar os utilizadores de um inquilino do Azure AD. |
| C# / .NET |[WebApp-multi-inquilino-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) |Um multi-inquilino aplicação de web de MVC do .NET que utiliza o OpenID Connect (ASP.Net OpenID Connect OWIN middleware) para autenticar os utilizadores a partir de múltiplos inquilinos do Azure AD. |
| C# / .NET |[WebApp-WSFederation-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) |Utilize o WS-Federation (ASP.Net WS-Federation OWIN middleware) para autenticar os utilizadores de um inquilino do Azure AD. |

## <a name="single-page-application-spa"></a>Aplicação de página única (SPA)
Este exemplo mostra como escrever uma aplicação de página única protegida com o Azure AD.  

| Idioma/plataforma | Exemplo | Descrição |
| --- | --- | --- |
| JavaScript, c# / .NET |[SinglePageApp-DotNet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) |Utilize a ADAL para JavaScript e o Azure AD para proteger uma aplicação de página única com base em AngularJS implementada com um ASP.NET web API de back-end. |

## <a name="native-application-to-web-api"></a>Aplicação nativa a API Web
Estes exemplos de código mostram como criar aplicações cliente nativo que chamar web APIs que estão protegidas pelo Azure AD. Utilizarem [do Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) e [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Idioma/plataforma | Exemplo | Descrição |
| --- | --- | --- |
| Javascript |[NativeClient-MultiTarget-Cordova](https://github.com/Azure-Samples/active-directory-cordova-multitarget) |Utilize o plug-in ADAL para Apache Cordova para criar uma aplicação Apache Cordova que chama uma API web e utiliza o Azure AD para autenticação. |
| C# / .NET |[NativeClient-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) |Uma aplicação .NET WPF que chama uma API web que está protegida por utilizar o Azure AD. |
| C# / .NET |[NativeClient WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) |Uma aplicação da loja Windows que chama uma API web que está protegida com o Azure AD. |
| C# / .NET |[NativeClient-end WebAPI multi-inquilino WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) |Uma aplicação da loja Windows chamar uma API que está protegida com o Azure AD de web do multi-inquilino. |
| C# / .NET |[End WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) |Uma aplicação de cliente nativo que chama uma API, que obtém um token para agir em nome de utilizador original e, em seguida, utiliza o token para chamar outra API web do web. |
| C# / .NET |[NativeClient WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) |Uma aplicação da loja Windows para Windows Phone 8.1 que chama uma API web que está protegida pelo Azure AD. |
| ObjC |[NativeClient iOS](https://github.com/Azure-Samples/active-directory-ios) |Uma aplicação iOS que chama uma API web que requer o Azure AD para autenticação. |
| C# / .NET |[End WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) |Uma aplicação cliente nativa que inclui uma lógica para processar um token JWT numa API, web em vez de utilizar o OWIN middleware. |
| C# / Xamarin |[NativeClient-Xamarin-Android](https://github.com/Azure-Samples/active-directory-xamarin-android) |Um enlace de Xamarin para o nativo do Azure AD Authentication Library (ADAL) para a biblioteca do Android. |
| C# / Xamarin |[NativeClient-Xamarin-iOS](https://github.com/Azure-Samples/active-directory-xamarin-ios) |Um enlace de Xamarin para o nativo do Azure AD Authentication Library (ADAL) para iOS. |
| C# / Xamarin |[NativeClient-MultiTarget-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) |Um projeto Xamarin que visa cinco plataformas e chama uma API web que está protegido pelo Azure AD. |
| C# / .NET |[NativeClient sem interface DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) |Uma aplicação nativa que efetua a autenticação não interativa e chama uma API web que está protegida pelo Azure AD. |

## <a name="web-application-to-web-api"></a>Web API da aplicação Web
Estes exemplos de código mostram como utilizar [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) para criar aplicações web que web de chamada de APIs que estão protegidas pelo Azure AD.

| Idioma/plataforma | Exemplo | Descrição |
| --- | --- | --- |
| C# / .NET |[WebApp-end WebAPI-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) |Chame uma API web com permissões com sessão iniciada do utilizador. |
| C# / .NET |[WebApp-end WebAPI-OAuth2-AppIdentity-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) |Chame uma API web com permissões da aplicação. |
| C# / .NET |[WebApp-end WebAPI-OAuth2-UserIdentity-Dotnet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) |Adicionar autorização com [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) para uma aplicação web existente para que possa chamar uma API web. |
| JavaScript |[End WebAPI Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) |Configure um serviço de REST API que está integrado com o Azure AD para proteção de API. Inclui um servidor de Node.js com uma API Web. |
| C# / .NET |[WebApp-end WebAPI-multi-inquilino-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) |Um multi-inquilino MVC web de aplicação que utiliza o OpenID Connect (ASP.Net OpenID Connect OWIN middleware) para autenticar os utilizadores de um inquilino do Azure AD. Utiliza um código de autorização para invocar a Graph API. |

## <a name="server-or-daemon-application-to-web-api"></a>Servidor ou aplicação Daemon API Web
Estes exemplos de código mostram como criar uma aplicação de daemon ou servidor que obtém os recursos de uma API web utilizando [do Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) e [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Idioma/plataforma | Exemplo | Descrição |
| --- | --- | --- |
| C# / .NET |[O daemon de DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) |Uma aplicação de consola chama uma API web. A credencial de cliente é uma palavra-passe. |
| C# / .NET |[O daemon-CertificateCredential-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) |Uma aplicação de consola que chama uma API web. A credencial de cliente é um certificado. |

## <a name="calling-azure-ad-graph-api"></a>Chamar do Azure AD Graph API
Nestes exemplo de código mostram como criar aplicações que chamar a API do Azure AD Graph para ler e escrever dados de diretório.

| Idioma/plataforma | Exemplo | Descrição |
| --- | --- | --- |
| Java |[WebApp-GraphAPI-Java](https://github.com/Azure-Samples/active-directory-java-graphapi-web) |Uma aplicação web que utilize a Graph API para aceder a dados do diretório do Azure AD. |
| PHP |[WebApp-GraphAPI-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-web) |Uma aplicação web que utilize a Graph API para aceder a dados do diretório do Azure AD. |
| C# / .NET |[WebApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) |Uma aplicação web que utilize a Graph API para aceder a dados do diretório do Azure AD. |
| C# / .NET |[ConsoleApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-console) |Esta aplicação de consola demonstra comuns chamadas de leitura e escrita para a Graph API e mostra como executar a atribuição de licenças de utilizador e de atualização de um utilizador fotografia em miniatura e ligações. |
| C# / .NET |[ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) |Uma aplicação de consola que utiliza a consulta diferencial na Graph API para obter periódicas alterações a objetos de utilizador de um inquilino do Azure AD. |
| C# / .NET |[WebApp-GraphAPI-DirectoryExtensions-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) |Uma aplicação MVC utiliza consultas de Graph API para gerar um gráfico de organizacional simples da empresa. |
| PHP |[WebApp GraphAPI-DirectoryExtensions PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) |Uma aplicação PHP que chama a Graph API para registar uma extensão e, em seguida, ler, atualizar e eliminar os valores no atributo de extensão. |

## <a name="authorization"></a>Autorização
Estes exemplos de código mostram como utilizar o Azure AD para autorização.

| Idioma/plataforma | Exemplo | Descrição |
| --- | --- | --- |
| C# / .NET |[WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) |Efetue o controlo de acesso baseado em funções (RBAC) utilizando afirmações de grupo do Active Directory do Azure numa aplicação que está integrada com o Azure AD. |
| C# / .NET |[WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) |Efetue o controlo de acesso baseado em funções (RBAC) utilizando as funções do Azure Active Directory da aplicação numa aplicação que está integrada com o Azure AD. |

## <a name="legacy-walkthroughs"></a>Instruções legadas
Estas instruções utilizam a tecnologia ligeiramente mais antiga, mas ainda podem ser de interesse.

| Idioma/plataforma | Exemplo | Descrição |
| --- | --- | --- |
| C# / .NET |[Autorização baseada em funções e baseada no ACL numa aplicação do Microsoft Azure AD](http://go.microsoft.com/fwlink/?LinkId=331694) |Efetue autorização baseada em funções (RBAC) e de autorização baseada em ACL numa aplicação que está integrada com o Azure AD. |
| C# / .NET |[AAL - aplicação da loja Windows para o serviço REST - autenticação](http://go.microsoft.com/fwlink/?LinkId=330605) |Utilize [do Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) (anteriormente AAL) para Windows Store Beta adicionar capacidades de autenticação de utilizador para uma aplicação da loja Windows. |
| C# / .NET |[Autenticação ADAL - aplicação nativa para serviço REST - com o AAD através do diálogo do Browser](http://go.microsoft.com/fwlink/?LinkId=259814) |Utilize [do Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) para adicionar capacidades de autenticação de utilizador para um cliente do WPF. |
| C# / .NET |[Autenticação ADAL - aplicação nativa para serviço REST - com ACS através do diálogo do Browser](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) |Utilize [do Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) e [2.0 de serviço de controlo de acesso (ACS)](http://msdn.microsoft.com/library/azure/hh147631.aspx) para adicionar capacidades de autenticação de utilizador para um cliente do WPF. |
| C# / .NET |[ADAL - autenticação de servidor para servidor](http://go.microsoft.com/fwlink/?LinkId=259816) |Utilize [do Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) para proteger as chamadas de serviço a partir de um processo do lado do servidor a um serviço MVC4 Web API REST. |
| C# / .NET |[Adicionar início de sessão à aplicação Web através do Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) |Configure uma aplicação .NET para executar web-início de sessão único contra o diretório de empresa do Azure AD. |
| C# / .NET |[Desenvolver aplicações Web do multi-inquilino com o Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) |Utilize o Azure AD para adicionar o início de sessão único e capacidades de acesso de diretório de uma aplicação .NET para trabalhar em várias organizações. |
| JAVA |[Aplicação de exemplo de Java para o Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkId=263969) |Utilize a Graph API para aceder a dados do diretório do Azure AD. |
| PHP |[Aplicação de exemplo do PHP para o Azure AD Graph API](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) |Utilize a Graph API para aceder a dados do diretório do Azure AD. |
| C# / .NET |[Aplicação de exemplo para o Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkID=262648) |Utilize a Graph API para aceder a dados do diretório do Azure AD. |
| C# / .NET |[Aplicação de exemplo para consulta diferencial do Azure AD Graph](http://go.microsoft.com/fwlink/?LinkId=275398) |Utilize a consulta diferencial a Graph API para obter periódicas alterações a objetos de utilizador de um inquilino do Azure AD. |
| C# / .NET |[Aplicação de exemplo para integrar a aplicação multi-inquilino de nuvem do Azure AD](http://go.microsoft.com/fwlink/?LinkId=275397) |Integre uma aplicação multi-inquilino do Azure AD. |
| C# / .NET |[Proteger uma aplicação da loja Windows e o serviço Web REST utilizar o Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) |Criar um recurso web simples API e uma aplicação de cliente da loja Windows através do Azure AD e o [do Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md). |
| C# / .NET |[Utilizar a Graph API para consultar o Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) |Configure uma aplicação do Microsoft .NET para utilizar a AD Graph API do Azure para aceder a dados a partir de um diretório de inquilino do Azure AD. |

## <a name="see-also"></a>Consultar também
##### <a name="other-resources"></a>Outros Recursos
[Guia para programadores do Azure Active Directory](active-directory-developers-guide.md)

[Azure AD Graph API Conceptual e de referência](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteca do Azure AD Graph API do programa auxiliar](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
