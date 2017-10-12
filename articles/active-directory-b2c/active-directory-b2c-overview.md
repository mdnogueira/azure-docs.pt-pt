---
title: "Descrição Geral - Azure AD B2C | Microsoft Docs"
description: "Desenvolver aplicações direcionadas para o consumidor com o Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: saeedakhter-msft
ms.openlocfilehash: 44d5d31d49c375c802a67511d1f962df20656559
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-focus-on-your-app-let-us-worry-about-sign-up-and-sign-in"></a>Azure AD B2C: concentre-se na sua aplicação; deixe que sejamos nós a preocuparmo-nos com a inscrição e o início de sessão

O Azure AD B2C é uma solução de gestão de identidades na cloud para as suas aplicações Web e móveis. É um serviço global com elevada disponibilidade que se pode dimensionar para centenas de milhões de identidades. Criado com base numa plataforma segura de nível empresarial, o Azure AD B2C mantém as suas aplicações, a sua empresa e os seus clientes protegidos.

Com configuração mínima, o Azure AD B2C permite à sua aplicação autenticar:

* **Contas de Redes Sociais** (como o Facebook, o Google, o LinkedIn, entre outras)
* **Contas Empresariais** (através de protocolos abertos padrão, OpenID Connect ou SAML)
* **Contas Locais** (endereço e palavra-passe de e-mail ou nome de utilizador e palavra-passe)

## <a name="get-started"></a>Introdução

Primeiro, obtenha o seu inquilino, utilizando os passos descritos em [Create an Azure AD B2C tenant](active-directory-b2c-get-started.md) (Criar um inquilino do Azure AD B2C).

Em seguida, escolha o seu cenário de desenvolvimento de aplicações:

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Aplicações Móveis e de Ambiente de Trabalho](../active-directory/develop/media/active-directory-developers-guide/NativeApp_Icon.png)<br />Aplicações Móveis e de Ambiente de Trabalho</center> | [Descrição Geral](active-directory-b2c-reference-oauth-code.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br /><br />[iOS](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)<br /><br />[Android](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal) | [.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)<br /><br />[Xamarin](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) |  |
| <center>![Aplicações Web](../active-directory/develop/media/active-directory-developers-guide/Web_app.png)<br />Aplicações Web</center> | [Descrição geral](active-directory-b2c-reference-oidc.md)<br /><br />[ASP.NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)<br /><br />[Núcleo do ASP.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp) | [Node.js](active-directory-b2c-devquickstarts-web-node.md) |  |
| <center>![Aplicações de Página Única](../active-directory/develop/media/active-directory-developers-guide/SPA.png)<br />Aplicações de Página Única</center> | [Descrição geral](active-directory-b2c-reference-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)<br /><br /> |  |  |
| <center>![APIs da Web](../active-directory/develop/media/active-directory-developers-guide/Web_API.png)<br />APIs da Web</center> | [ASP.NET](active-directory-b2c-devquickstarts-api-dotnet.md)<br /><br /> [Núcleo do ASP.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)<br /><br /> [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | [Chamar uma API Web .NET](active-directory-b2c-devquickstarts-web-api-dotnet.md) |

## <a name="whats-new"></a>Novidades

Verifique novamente aqui com frequência para saber mais sobre as alterações futuras ao Azure Active Directory B2C. Também tweetamos as eventuais atualizações com @AzureAD.

* Para além das “Políticas Incorporadas” (Disponibilidade Geral), a funcionalidade [“Políticas Personalizadas”](active-directory-b2c-overview-custom.md) está agora disponível em pré-visualização pública.  As políticas personalizadas destinam-se a profissionais de identidades que precisam de ter controlo sobre a composição das respetivas experiências de identidade.
* A funcionalidade [Token de Acesso](https://azure.microsoft.com/en-us/blog/azure-ad-b2c-access-tokens-now-in-public-preview) está agora disponível em pré-visualização pública.
* Foi anunciada a [Disponibilidade Geral dos diretórios do Azure AD B2C baseados na Europa](https://azure.microsoft.com/en-us/blog/azuread-b2c-ga-eu/).
* Veja a nossa crescente biblioteca de [exemplos de códigos no GitHub](https://github.com/Azure-Samples?q=b2c)!

## <a name="how-to-articles"></a>Artigos de procedimentos

Saiba como utilizar funcionalidades específicas do Azure Active Directory B2C:

* Configurar contas [Facebook](active-directory-b2c-setup-fb-app.md), [Google +](active-directory-b2c-setup-goog-app.md), [conta Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) e [LinkedIn](active-directory-b2c-setup-li-app.md) para utilização nas aplicações direcionadas para o consumidor.
* [Utilizar atributos personalizados para recolher informações sobre os consumidores](active-directory-b2c-reference-custom-attr.md).
* [Ativar a multi-Factor Authentication do Azure nas aplicações direcionadas para o consumidor](active-directory-b2c-reference-mfa.md).
* [Configurar a reposição de palavras-passe self-service para os consumidores](active-directory-b2c-reference-sspr.md).
* [Personalizar o aspeto e a funcionalidade das páginas de inscrição, início de sessão e outras páginas destinadas ao consumidor](active-directory-b2c-reference-ui-customization.md) que são disponibilizadas pelo Azure Active Directory B2C.
* [Utilizar a Graph API do Azure Active Directory para programaticamente criar, ler, atualizar e eliminar consumidores](active-directory-b2c-devquickstarts-graph-dotnet.md) no seu inquilino do Azure Active Directory B2C.

## <a name="next-steps"></a>Passos seguintes

Estas ligações são úteis para explorar o serviço em profundidade:

* Veja as [Informações sobre preços do Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
* Veja os nossos [exemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c) do Azure Active Directory B2C. 
* Obtenha ajuda na Stack Overflow, utilizando as etiqueta [azure-ad-b2c](http://stackoverflow.com/questions/tagged/azure-ad-b2c).
* Indique-nos a sua opinião sobre como utilizar [A Voz do Utilizador](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c), queremos ouvi-la!
* Reveja a [Referência de Protocolo do Azure AD B2C](active-directory-b2c-reference-protocols.md).
* Reveja a [Referência de Token do Azure AD B2C](active-directory-b2c-reference-tokens.md).
* Leia as [Perguntas mais frequentes do Azure Active Directory B2C](active-directory-b2c-faqs.md).
* [Pedidos de suporte de ficheiros para o Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança dos nossos produtos

Aconselhamo-lo a obter notificações de quando os incidentes de segurança ocorrem, visitando [esta página](https://technet.microsoft.com/security/dd252948) e subscrevendo os alertas de aviso de segurança.

