---
title: Azure Active Directory para programadores | Microsoft Docs
description: "Este artigo fornece uma descrição geral sobre o início de sessão nas contas profissionais e escolares da Microsoft através do Azure Active Directory."
services: active-directory
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: dce813b885d492343428428056a2e8aada27b461
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory para programadores
O Azure Active Directory é um serviço de identidade em cloud que permite que os programadores iniciem a sessão de qualquer utilizador de forma segura com uma conta profissional ou escolar ,segura pela Microsoft.  A documentação presente mostra-lhe como adicionar suporte do Azure AD à aplicação através de protocolos de autenticação padrão da indústria, o OAuth e o OpenID Connect.

| | |
| --- | --- |
|[Noções básicas da autenticação](active-directory-authentication-scenarios.md) | Introdução à autenticação com o Azure AD |
|[Tipos de aplicações](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Descrição geral dos cenários autenticação suportado pelo Azure AD |                                
                                                                              
## <a name="get-started"></a>Introdução
Estas configurações assistidas vão guiá-lo através da utilização das bibliotecas de autenticação para iniciar a sessão dos utilizadores do Azure Active Directory.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Aplicações Móveis e de Ambiente de Trabalho](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Aplicações Móveis e de Ambiente de Trabalho</center> | [Descrição geral](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET](active-directory-devquickstarts-dotnet.md)<br /><br />[Windows](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md)<br /><br />[OAuth 2.0](active-directory-protocols-oauth-code.md) |
| <center>![Aplicações Web](./media/active-directory-developers-guide/Web_app.png)<br />Aplicações Web</center> | [Descrição geral](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [NodeJS](active-directory-devquickstarts-openidconnect-nodejs.md)<br /><br />[OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) |  |
| <center>![Aplicações de Página Única](./media/active-directory-developers-guide/SPA.png)<br />Aplicações de Página Única</center> | [Descrição geral](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![APIs da Web](./media/active-directory-developers-guide/Web_API.png)<br />APIs da Web</center> | [Descrição geral](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[NodeJS](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Serviço a serviço](./media/active-directory-developers-guide/Service_App.png)<br />Serviço a serviço</center> | [Descrição geral](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)<br /><br />[Credenciais de Cliente OAuth 2.0](active-directory-protocols-oauth-service-to-service.md) |  |

## <a name="guides"></a>Guias
Estes artigos informam-no sobre como realizar tarefas comuns com o Azure Active Directory.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Registo da aplicação](active-directory-integrating-applications.md)           | Como registar uma aplicação no Azure AD |
|[Aplicações multi-inquilino](active-directory-devhowto-multi-tenant-overview.md)    | Como iniciar sessão numa conta profissional Microsoft |
|[OAuth e OpenID Connect](active-directory-protocols-openid-connect-code.md)| Como iniciar a sessão de utilizadores e chamar APIs Web utilizando protocolos de autenticação moderna |
|[Mais guias...](active-directory-developers-guide-index.md#guides)        |     |

## <a name="reference"></a>Referência
Estes artigos fornecem informações detalhadas sobre APIs, mensagens de protocolo e termos utilizados no Azure Active Directory.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Bibliotecas de Autenticação (ADAL)](active-directory-authentication-libraries.md)   | Descrição geral de bibliotecas e SDKs fornecidos pelo Azure AD |
| [Exemplos de Código](active-directory-code-samples.md)                                  | Lista de todos os exemplos de código do Azure AD |
| [Glossário](active-directory-dev-glossary.md)                                      | Terminologia e definições de palavras utilizadas nesta documentação |
| [Mais m...](active-directory-developers-guide-index.md#reference)|     |

## <a name="help--support"></a>Ajuda e Suporte
Estes são os melhores locais para obter ajuda com o desenvolvimento do Azure Active Directory.

|  |  
|---|
|[Etiquetas do Stack Overflow `azure-active-directory` e `adal`](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)      |
|[Comentários sobre o Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)|
| [Experimente o Microsoft Dev Chat (gratuito por um período limitado)](http://aka.ms/devchat) |

<br />

> [!NOTE]
> Caso precise de iniciar sessão em contas pessoais Microsoft, poderá querer considerar a utilização do [ponto final do Azure AD v2.0](active-directory-appmodel-v2-overview.md).  O ponto final do Azure ADv 2.0 é unificação de contas pessoais e contas profissionais da Microsoft (a partir do Azure AD) num sistema de autenticação único.
