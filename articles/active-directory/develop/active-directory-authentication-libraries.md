---
title: "Bibliotecas de autenticação do Azure Active Directory | Microsoft Docs"
description: "O Azure AD Authentication Library (ADAL) permite aos programadores de aplicações autenticar facilmente os utilizadores para a nuvem de cliente ou no local do Active Directory (AD) e, em seguida, obter os tokens de acesso para proteger a chamadas de API."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/25/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 1b79fb5b280b0cb4e087c2acde07796fd51e81fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-authentication-libraries"></a>Bibliotecas de autenticação do Azure Active Directory
O Active Directory Authentication Library (ADAL) do Azure permite aos programadores da aplicação autenticar os utilizadores na nuvem ou no local do Active Directory (AD) e obter os tokens para proteger a chamadas de API. ADAL facilita a autenticação para os programadores através de funcionalidades, tais como:
 - Cache de token configurável que arquivos de tokens de acesso e tokens de atualização
 - atualização automática de token quando um token de acesso expira e um token de atualização está disponível
 - suporte para chamadas de método assíncronas
 - e muito mais

> [!NOTE]
> Procurar as bibliotecas de v 2.0 do Azure AD (MSAL)? Finalizar a [guia da biblioteca MSAL](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-libraries). 
> 
> 

### <a name="client-libraries"></a>Bibliotecas de Cliente

| Plataforma | Biblioteca | Transferência | Código de origem | Exemplo | Referência
| --- | --- | --- | --- | --- | --- |
| Cliente de .NET, Loja Windows, UWP, Xamarin iOS e Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplicação de ambiente de trabalho](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referência](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) | 
| .NET cliente, a loja Windows, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Aplicação de ambiente de trabalho](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | | 
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Aplicação de página única](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[aplicação iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referência](https://cocoapods.org/pods/ADAL)|
| Android |ADAL |[O repositório Central](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplicação Android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Aplicação Web Java](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-java) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) | | |

### <a name="server-libraries"></a>Servidor de bibliotecas 

| Plataforma | Biblioteca | Transferência | Código de origem | Exemplo | Referência
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN para AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicação MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN para OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicação Web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Passport do Azure AD |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [API Web](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN para WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicação Web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Extensões de protocolo de identidade para o .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Processador JWT para o .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |

### <a name="v20-client-libraries-msal"></a>Bibliotecas de cliente de v 2.0 (MSAL)

O [ponto final de v 2.0 do Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-compare) combina do Azure AD e Microsoft Accounts atrás de um único ponto final. Para aceder a este ponto final, os programadores podem utilizar o [bibliotecas MSAL suportado de produção pré-visualização](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-libraries) em vez da ADAL.

| Plataforma | Biblioteca | Transferência | Código de origem | Exemplo | Referência
| --- | --- | --- | --- | --- | --- |
| Cliente de .NET, Loja Windows, UWP, Xamarin iOS e Android |MSAL para .NET (pré-visualização) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/1.1.0-preview) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplicação de ambiente de trabalho](~/articles/active-directory/develop/guidedsetups/active-directory-windesktop.md) |[Referência](https://docs.microsoft.com/dotnet/api/?view=identityclient-1.1.0-preview) | 
| JavaScript |MSAL para JavaScript (pré-visualização) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Aplicação de página única](~/articles/active-directory/develop/GuidedSetups/active-directory-javascriptspa.md) | [Referência](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/docs/classes/_useragentapplication_.msal.useragentapplication.html) | 
| iOS |MSAL para iOS (pré-visualização) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [aplicação iOS](~/articles/active-directory/develop/GuidedSetups/active-directory-ios.md) | [Referência](https://azuread.github.io/docs/objc/) |
| Android |MSAL para Android (pré-visualização) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplicação Android](~/articles/active-directory/develop/GuidedSetups/active-directory-android.md) | [Referência](http://javadoc.io/doc/com.microsoft.identity.client/msal/0.1.1) |

## <a name="scenarios"></a>Cenários

Seguem-se três cenários comuns nos quais ADAL pode ser utilizado para autenticar um cliente que acede a um recurso remoto:  

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Autenticação de utilizadores de uma aplicação de cliente nativo em execução num dispositivo 

Neste cenário, um programador tem uma aplicação de cliente do WPF, que tem de aceder a um recurso remoto protegido pelo Azure AD, como uma API web. Ron tem uma subscrição do Azure, sabe como invocar a API web a jusante e sabe o inquilino do Azure AD que utiliza a API web. Como resultado, ele pode utilizar a ADAL para facilitar a autenticação com o Azure AD, por totalmente delegar a experiência de autenticação adal ou por processe explicitamente as credenciais do utilizador. ADAL faz com que facilitam a autenticar o utilizador, obter um token de acesso e o token de atualização do Azure AD e, em seguida, utilizar o token de acesso para efetuar pedidos para a API web.

Para um código de exemplo que demonstra este cenário através de autenticação para o Azure AD, consulte [aplicação nativa do WPF de cliente para Web API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Autenticação de uma aplicação de cliente confidencial em execução num servidor web

Neste cenário, um programador tem uma aplicação em execução num servidor que tem de aceder a um recurso remoto protegido pelo Azure AD, como uma API web. Ron tem uma subscrição do Azure, sabe como invocar o serviço a jusante e sabe que utiliza a API web de inquilino do Azure AD. Como resultado, ele pode utilizar a ADAL para facilitar a autenticação com o Azure AD, processando explicitamente as credenciais da aplicação. ADAL faz com que facilitam a obter um token do Azure AD através da utilização de credencial de cliente da aplicação e, em seguida, utilizar esse token para fazer pedidos para a API web. ADAL também faz a gestão da duração do token de acesso através da colocação em cache-lo e renová-la conforme for necessário. Para um código de exemplo que demonstra neste cenário, consulte [consola Daemon aplicação Web API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Uma aplicação de cliente confidencial em execução num servidor, em nome de um utilizador a autenticar 

Neste cenário, um programador tem uma aplicação em execução num servidor que tem de aceder a um recurso remoto protegido pelo Azure AD, como uma API web. O pedido também tem de ser efetuadas em nome de um utilizador do Azure AD. Ron tem uma subscrição do Azure, sabe como invocar a API web a jusante e sabe o Azure AD que utiliza o serviço de inquilino. Depois do utilizador é autenticado para a aplicação web, a aplicação pode obter um código de autorização para o utilizador a partir do Azure AD. A aplicação web, em seguida, pode utilizar a ADAL para obter um token de acesso e atualizar o token em nome de um utilizador com as credenciais de cliente e código de autorização associadas à aplicação do Azure AD. Assim que a aplicação web estiver na posse do token de acesso, pode chamar a API web até que o token expira. Quando o token expira, a aplicação web pode utilizar a ADAL para obter um novo token de acesso utilizando a atualização de token que foi anteriormente recebido. Para um código de exemplo que demonstra neste cenário, consulte [Native client a API Web para Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Veja Também

- [Guia para programadores do Azure Active Directory](active-directory-developers-guide.md)
- [Cenários de autenticação para o Azure Active directory](active-directory-authentication-scenarios.md)
- [Exemplos de código do Azure Active Directory](active-directory-code-samples.md)
