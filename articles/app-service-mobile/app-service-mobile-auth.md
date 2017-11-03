---
title: "Autenticação e autorização em Mobile Apps do Azure | Microsoft Docs"
description: "Referência conceptual e descrição geral sobre a autenticação / autorização da funcionalidade para Mobile Apps do Azure"
services: app-service\mobile
documentationcenter: 
author: mattchenderson
manager: syntaxc4
editor: 
ms.assetid: a46dbf70-867d-48f6-8885-7f5207ad102e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 4105392f58eaf37e88c1d9ffb74f3f4133fa5482
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="authentication-and-authorization-in-azure-mobile-apps"></a>Autenticação e Autorização nas Aplicações Móveis do Azure
## <a name="what-is-app-service-authentication--authorization"></a>O que é a aplicação serviço de autenticação / autorização?
> [!NOTE]
> Este tópico será migrado para uma consolidado [aplicação serviço de autenticação / autorização](../app-service/app-service-authentication-overview.md) tópico, que abrange Web, móveis e API Apps.
> 
> 

Autenticação do serviço de aplicação / autorização é uma funcionalidade que permite à aplicação de início de sessão utilizadores sem alterações de código necessárias no back-end da aplicação. Fornece uma forma fácil para proteger a sua aplicação e trabalhar com dados por utilizador.

Serviço de aplicações utiliza a identidade federada, no qual um terceiros 3rd **fornecedor de identidade** ("IDP") armazena contas e autentica utilizadores e a aplicação utiliza esta identidade em vez dos seus próprios. Serviço de aplicações suporta cinco fornecedores de identidade a Box: *do Azure Active Directory*, *Facebook*, *Google*, *Account Microsoft*, e *Twitter*. Também pode expandir este suporte para as suas aplicações através da integração de outro fornecedor de identidade ou a sua própria solução de identidade personalizada.

A aplicação, pode tirar partido qualquer número destes fornecedores de identidade, para que possam fornecer aos utilizadores finais com opções para como poderem iniciar sessão.

Se pretender começar imediatamente, consulte um dos seguintes tutoriais:

* [Adicionar autenticação à sua aplicação iOS]
* [Adicionar autenticação à sua aplicação xamarin. IOS]
* [Adicionar autenticação à sua aplicação xamarin. Android]
* [Adicionar autenticação à sua aplicação do Windows]

## <a name="how-authentication-works"></a>Como funciona a autenticação
Para autenticar através de um dos fornecedores de identidade, terá primeiro de configurar o fornecedor de identidade saber sobre a sua aplicação. O fornecedor de identidade, em seguida, irá fornecer IDs e segredos que fornecem novamente à aplicação. Isto conclui a relação de confiança e permite que o serviço de aplicações validar as identidades fornecidas ao mesmo.

Estes passos estão descritos nos tópicos seguintes:

* [Como configurar a sua aplicação para utilizar o início de sessão do Azure Active Directory]
* [Como configurar a sua aplicação para utilizar o início de sessão do Facebook]
* [Como configurar a sua aplicação para utilizar o início de sessão do Google]
* [Como configurar a sua aplicação para utilizar o início de sessão do Microsoft Account]
* [Como configurar a sua aplicação para utilizar o início de sessão do Twitter]

Assim que tudo está configurado no back-end, pode modificar o cliente para iniciar sessão. Existem duas abordagens aqui:

* Utilizar uma única linha de código, permitem que as Mobile Apps cliente SDK aos utilizadores iniciar sessão.
* Tire partido de um SDK publicado por um fornecedor de identidade fornecido para estabelecer a identidade e, em seguida, obter acesso ao serviço de aplicações.

> [!TIP]
> A maioria das aplicações devem utilizar um fornecedor de SDK para obter uma experiência de início de sessão mais nativo-feeling e tirar partido do suporte de atualização e outras vantagens específica do fornecedor.
> 
> 

### <a name="how-authentication-without-a-provider-sdk-works"></a>Como funciona a autenticação sem um fornecedor de SDK
Se não pretender configurar um fornecedor de SDK, pode permitir que as Mobile Apps efetuar o início de sessão para si. O SDK do cliente de Mobile Apps irá abrir uma vista web para o fornecedor da sua escolha e concluir o início de sessão. Ocasionalmente, blogues e fóruns verá isto referido como "fluxo de servidor" ou "direcionadas para o servidor de fluxo" dado que o servidor está a gerir o início de sessão e o cliente SDK nunca recebe o token de fornecedor.

O código necessário para iniciar este fluxo é descrito no tutorial autenticação para cada plataforma. No final do fluxo, o cliente SDK tem um token de serviço de aplicações e o token é automaticamente anexado a todos os pedidos para o back-end.

### <a name="how-authentication-with-a-provider-sdk-works"></a>Como funciona a autenticação com um fornecedor de SDK
Trabalhar com um fornecedor de SDK permite que a experiência de início de sessão mais rigidamente interajam com a plataforma de SO que a aplicação está em execução. Isto também fornece um token de fornecedor e algumas informações de utilizador no cliente, o que torna mais fácil e consumir APIs do gráfico e personalizar a experiência de utilizador. Ocasionalmente em blogues e fóruns verá isto referido como o fluxo"cliente" ou "direcionadas para o cliente fluxo" desde o código no cliente está a processar o início de sessão e o código de cliente tem acesso a um token de fornecedor.

Depois de um token de fornecedor é obtido, tem de ser enviadas para o App Service para validação. No final do fluxo, o cliente SDK tem um token de serviço de aplicações e o token é automaticamente anexado a todos os pedidos para o back-end. Os programadores também podem manter uma referência para o token de fornecedor se escolherem, por isso.

## <a name="how-authorization-works"></a>Como funciona a autorização
Autenticação do serviço de aplicação / autorização expõe várias opções para **ação a tomar quando o pedido não é autenticado**. Antes do seu código recebe um pedido específico, pode ter verificação do serviço de aplicações para ver se o pedido é autenticado e se não, rejeitá-lo e tentar possui o utilizador iniciar sessão antes de tentar novamente.

Uma das alternativas consiste em ter não autenticados redireccionamento de pedidos para um dos fornecedores de identidade. Num browser, isto, na verdade, de efetuar uma nova página ao utilizador. No entanto, o cliente móvel não pode ser redirecionado desta forma, e não autenticadas respostas irão receber um HTTP *não autorizado 401* resposta. Tendo em conta esta, o primeiro pedido que faz com que o seu cliente deve ser sempre para o ponto final de início de sessão e, em seguida, pode efetuar chamadas para quaisquer outras APIs. Se tentar chamar outra API antes de iniciar sessão, o cliente irá receber um erro.

Se pretender ter mais granular controlar ao longo do que os pontos finais exigir autenticação, também pode escolher "nenhuma ação (pedidos de permissões)" para pedidos não autenticados. Neste caso, todas as decisões de autenticação são adiadas ao código da aplicação. Isto também permite-lhe permitir o acesso a utilizadores específicos, com base nas regras de autorização personalizadas.

## <a name="documentation"></a>Documentação
Os tutoriais seguintes mostram como adicionar autenticação para os seus clientes móveis através do serviço de aplicações:

* [Adicionar autenticação à sua aplicação iOS]
* [Adicionar autenticação à sua aplicação xamarin. IOS]
* [Adicionar autenticação à sua aplicação xamarin. Android]
* [Adicionar autenticação à sua aplicação do Windows]

Os tutoriais seguintes mostram como configurar o serviço de aplicações para tirar partido dos fornecedores de autenticação diferentes:

* [Como configurar a sua aplicação para utilizar o início de sessão do Azure Active Directory]
* [Como configurar a sua aplicação para utilizar o início de sessão do Facebook]
* [Como configurar a sua aplicação para utilizar o início de sessão do Google]
* [Como configurar a sua aplicação para utilizar o início de sessão do Microsoft Account]
* [Como configurar a sua aplicação para utilizar o início de sessão do Twitter]

Se pretender utilizar um sistema de identidade sem ser aqueles fornecido aqui, pode também tirar partido do [pré-visualizar suporte de autenticação personalizado no servidor de .NET SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth).

[Adicionar autenticação à sua aplicação iOS]: app-service-mobile-ios-get-started-users.md
[Adicionar autenticação à sua aplicação xamarin. IOS]: app-service-mobile-xamarin-ios-get-started-users.md
[Adicionar autenticação à sua aplicação xamarin. Android]: app-service-mobile-xamarin-android-get-started-users.md
[Adicionar autenticação à sua aplicação do Windows]: app-service-mobile-windows-store-dotnet-get-started-users.md

[Como configurar a sua aplicação para utilizar o início de sessão do Azure Active Directory]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Como configurar a sua aplicação para utilizar o início de sessão do Facebook]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Como configurar a sua aplicação para utilizar o início de sessão do Google]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[Como configurar a sua aplicação para utilizar o início de sessão do Microsoft Account]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Como configurar a sua aplicação para utilizar o início de sessão do Twitter]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
