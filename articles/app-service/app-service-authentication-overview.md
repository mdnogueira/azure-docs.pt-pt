---
title: "Autenticação e autorização no serviço de aplicações do Azure | Microsoft Docs"
description: "Referência conceptual e descrição geral sobre a autenticação / autorização funcionalidades do App Service do Azure"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/29/2016
ms.author: mahender
ms.openlocfilehash: f0d2644903181cd2e20166feae4f90ddd4037fa8
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticação e autorização no Serviço de Aplicações do Azure
## <a name="what-is-app-service-authentication--authorization"></a>O que é a aplicação serviço de autenticação / autorização?
Autenticação do serviço de aplicação / autorização é uma funcionalidade que fornece uma forma para a sua aplicação iniciar sessão em utilizadores para que não tem de alterar o código no back-end da aplicação. Fornece uma forma fácil para proteger a sua aplicação e trabalhar com dados por utilizador.

Serviço de aplicações utiliza a identidade federada, em que um fornecedor de identidade de terceiros armazena contas e autentica utilizadores. A aplicação baseia-se nas informações de identidade do fornecedor para que a aplicação não tem de armazenar essas informações si próprio. Serviço de aplicações suporta cinco fornecedores de identidade a Box: Azure Active Directory, Facebook, Google, Account Microsoft e Twitter. A aplicação pode utilizar qualquer número destes fornecedores de identidade para fornecer os seus utilizadores com as opções para a forma de iniciar sessão. Para expandir o suporte incorporado, pode integrar outro fornecedor de identidade ou [sua própria solução de identidade personalizada][custom-auth].

Se pretender começar imediatamente, consulte um dos seguintes tutoriais [adicionar autenticação à sua aplicação iOS] [ iOS] (ou [Android], [Windows], [Xamarin. IOS], [xamarin. Android], [xamarin. Forms], ou [Cordova]).

## <a name="how-authentication-works-in-app-service"></a>Como funciona a autenticação no App Service
Para autenticar utilizando um dos fornecedores de identidade, terá primeiro de configurar o fornecedor de identidade saber sobre a sua aplicação. O fornecedor de identidade, em seguida, irá fornecer IDs e segredos que fornecem ao serviço de aplicações. Este passo conclui a relação de confiança, para que o serviço de aplicações pode validar asserções de utilizador, tais como os tokens de autenticação do fornecedor de identidade.

Para iniciar sessão um utilizador utilizando um destes fornecedores, o utilizador deve ser redirecionado para um ponto final que inicia sessão dos utilizadores para esse fornecedor. Se os clientes estão a utilizar um web browser, pode fazer com que o serviço de aplicações encaminhar automaticamente todos os utilizadores não autenticados para o ponto final que inicia sessão dos utilizadores. Caso contrário, terá de direcionar os seus clientes para `{your App Service base URL}/.auth/login/<provider>`, onde `<provider>` é um dos seguintes valores: aad, facebook, google, twitter ou microsoft. Cenários móveis e API são explicados em secções neste artigo.

Utilizadores que interagem com a sua aplicação através de um browser web tem um cookie definido para que pode permanecer autenticados à medida que navegam a sua aplicação. Para outros tipos de cliente, como mobile, um token de web JSON (JWT) que devem ser apresentadas as `X-ZUMO-AUTH` cabeçalho, será emitido para o cliente. O cliente de Mobile Apps SDKs processará isto por si. Em alternativa, um token de identidade do Azure Active Directory ou o token de acesso pode ser diretamente incluído a `Authorization` cabeçalho como um [token de portador](https://tools.ietf.org/html/rfc6750).

Serviço de aplicações irá validar qualquer cookie ou um token que emite a aplicação para autenticar os utilizadores. Para restringir quem pode aceder à sua aplicação, consulte o [autorização](#authorization) secção neste artigo.

### <a name="mobile-authentication-with-a-provider-sdk"></a>Autenticação móvel com um fornecedor de SDK
Depois de está tudo configurado no back-end, pode modificar os clientes móveis iniciar sessão com o serviço de aplicações. Existem duas abordagens aqui:

* Utilize um SDK que publica um fornecedor de identidade fornecido para estabelecer a identidade e, em seguida, obter acesso ao serviço de aplicações.
* Utilize uma única linha de código, para que o SDK do cliente de Mobile Apps pode iniciar sessão de utilizadores.

> [!TIP]
> A maioria das aplicações devem utilizar um fornecedor de SDK para obter uma experiência mais consistente quando os utilizadores iniciarem sessão, para utilizar o suporte de atualização bem como obter outras vantagens que especifica o fornecedor.
> 
> 

Quando utiliza um fornecedor de SDK, os utilizadores poderem iniciar sessão para uma experiência que integra-se mais rigidamente com o sistema operativo que a aplicação está em execução no. Isto também fornece um token de fornecedor e algumas informações de utilizador no cliente, o que torna mais fácil e consumir APIs do gráfico e personalizar a experiência de utilizador. Ocasionalmente em blogues e fóruns, verá isto referido como o fluxo"cliente" ou "fluxo direcionadas para o cliente", porque o código no cliente inicia em utilizadores e o código de cliente tem acesso a um token de fornecedor.

Depois de um token de fornecedor é obtido, tem de ser enviadas para o App Service para validação. Depois do serviço de aplicações valida o token, o App Service cria um novo token de serviço de aplicações, que é devolvido ao cliente. O SDK do cliente de Mobile Apps tem métodos de programa auxiliar para gerir este exchange e anexar automaticamente o token para todos os pedidos para o back-end da aplicação. Os programadores também podem manter uma referência para o token de fornecedor se escolherem por isso.

### <a name="mobile-authentication-without-a-provider-sdk"></a>Autenticação móvel sem um fornecedor de SDK
Se não pretender configurar um fornecedor de SDK, pode permitir que a funcionalidade de aplicações móveis do serviço de aplicações do Azure para iniciar sessão para si. O SDK do cliente de Mobile Apps irá abrir uma vista web para o fornecedor da sua escolha e o utilizador iniciar sessão. Ocasionalmente em blogues e fóruns, verá isto referido como "fluxo de servidor" ou "direcionadas para o servidor de fluxo" porque o servidor gere o processo que inicia sessão dos utilizadores e o cliente SDK nunca recebe o token de fornecedor.

Código para iniciar este fluxo está incluído no tutorial autenticação para cada plataforma. No final do fluxo, o cliente SDK tem um token de serviço de aplicações e o token é automaticamente anexado a todos os pedidos para o back-end da aplicação.

### <a name="service-to-service-authentication"></a>Autenticação serviço a serviço
Embora pode dar aos utilizadores acesso à sua aplicação, também pode confiar outra aplicação para chamar a suas próprias API. Por exemplo, pode ter uma aplicação web chamar uma API na outra aplicação web. Neste cenário, utilize as credenciais para uma conta de serviço, em vez das credenciais de utilizador para obter um token. Uma conta de serviço também é conhecido como um *principal de serviço* parlance do Azure Active Directory e autenticação que utiliza uma conta deste género também é conhecido como um cenário de serviços.

> [!IMPORTANT]
> Porque as aplicações móveis executado nos dispositivos cliente, as aplicações móveis fazer *não* contam como aplicações fidedignas e não deve utilizar um fluxo de principal de serviço. Em vez disso, devem utilizar um fluxo de utilizador que foi detalhado anteriormente.
> 
> 

Para cenários de serviços, o serviço de aplicações pode proteger a aplicação utilizando o Azure Active Directory. A aplicação de chamada apenas tem de fornecer um token de autorização principal de serviço do Azure Active Directory que é obtido, fornecendo o cliente ID e o segredo do Azure Active Directory do cliente. Um exemplo deste cenário que utiliza aplicações API do ASP.NET é explicado pelo tutorial, [autenticação principal para o serviço para aplicações da API] [apia-serviço].

Se pretender utilizar a autenticação do serviço de aplicações para processar um cenário de serviços, pode utilizar certificados de cliente ou a autenticação básica. Para obter informações sobre certificados de cliente no Azure, consulte [como para configurar TLS autenticação mútua para aplicações Web](app-service-web-configure-tls-mutual-auth.md). Para obter informações sobre a autenticação básica no ASP.NET, consulte [filtros de autenticação no ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

Autenticação de conta de serviço de uma aplicação de lógica de serviço de aplicações numa aplicação API é num caso especial que está detalhado na [utilizando a API personalizada alojada no App Service com as Logic apps](../logic-apps/logic-apps-custom-hosted-api.md).

## <a name="authorization"></a>Como funciona a autorização no serviço de aplicações
Ter controlo total sobre os pedidos que pode aceder à aplicação. Autenticação do serviço de aplicação / autorização pode ser configurada com qualquer um dos seguintes comportamentos:

* Permitir apenas autenticados pedidos para acederem a sua aplicação.
  
    Se o browser envia um pedido anónimo, o serviço de aplicações irá redirecionar para uma página para o fornecedor de identidade que escolher, para que os utilizadores podem iniciar sessão. Se o pedido for proveniente de um dispositivo móvel, a resposta devolvida é um HTTP *não autorizado 401* resposta.
  
    Com esta opção, não precisa de escrever qualquer código de autenticação de todo na sua aplicação. Se precisar de autorização melhorar, informações sobre o utilizador estão disponíveis para o seu código.
* Permitir que todos os pedidos para acederem a sua aplicação, mas validar pedidos autenticados e transferem informações de autenticação nos cabeçalhos de HTTP.
  
    Esta opção defers decisões de autorização para o código da aplicação. Fornece uma maior flexibilidade no processamento de pedidos anónimos, mas tem de escrever código.
* Permitir que todos os pedidos para acederem a sua aplicação e efetuar nenhuma ação nas informações de autenticação nos pedidos.
  
    Neste caso, a autenticação / autorização funcionalidade está desativada. As tarefas de autenticação e autorização são completamente até o código da aplicação.

Comportamentos anteriores são controlados mediante a **ação a tomar quando o pedido não é autenticado** opção no portal do Azure. Se optar por * * iniciar sessão com *nome do fornecedor* * *, todos os pedidos têm de ser autenticado. **Permitir que o pedido (nenhuma ação)** defers a decisão de autorização de código, mas ainda fornece informações de autenticação. Se pretende que o seu código processar tudo, pode desativar a autenticação / a funcionalidade de autorização.

## <a name="working-with-user-identities-in-your-application"></a>Trabalhar com identidades de utilizador na sua aplicação
Serviço de aplicações passa algumas informações de utilizador à sua aplicação através da utilização de cabeçalhos especiais. Pedidos externos proíbem estes cabeçalhos e será apenas se presente definido pela aplicação serviço de autenticação / autorização. Alguns cabeçalhos de exemplo incluem:

* X-MS-CLIENTE--NOME PRINCIPAL
* X-MS-PRINCIPAL-ID DE CLIENTE
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Código que é escrito em qualquer linguagem ou arquitetura pode obter as informações que necessita destes cabeçalhos. Para aplicações ASP.NET 4.6, o **ClaimsPrincipal** é automaticamente definido com os valores adequados.

A aplicação também poderá obter os detalhes de utilizador adicionais através de um HTTP GET no `/.auth/me` ponto final da sua aplicação. Um token válido que incluído com o pedido irá devolver um payload JSON com detalhes sobre o fornecedor que está a ser utilizado, o token do fornecedor subjacente e outras informações de utilizador. O servidor de aplicações móveis SDKs fornecem métodos de programa auxiliar para trabalhar com estes dados. Para obter mais informações, consulte [como utilizar o Azure Mobile Apps Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), e [trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="documentation-and-additional-resources"></a>Documentação e recursos adicionais
### <a name="identity-providers"></a>Fornecedores de identidade
Os tutoriais seguintes mostram como configurar o serviço de aplicações a utilizar fornecedores de autenticação diferentes:

* [Como configurar a sua aplicação para utilizar o início de sessão do Azure Active Directory][AAD]
* [Como configurar a sua aplicação para utilizar o início de sessão do Facebook][Facebook]
* [Como configurar a sua aplicação para utilizar o início de sessão do Google][Google]
* [Como configurar a sua aplicação para utilizar o início de sessão do Microsoft Account][MSA]
* [Como configurar a sua aplicação para utilizar o início de sessão do Twitter][Twitter]

Se pretender utilizar um sistema de identidade sem ser aqueles fornecido aqui, também pode utilizar o [pré-visualizar suporte de autenticação personalizado no servidor de aplicações .NET dos Mobile SDK][custom-auth], que pode ser utilizado em aplicações web, as aplicações móveis ou as API apps.

### <a name="mobile-applications"></a>Aplicações móveis
Os tutoriais seguintes mostram como adicionar autenticação para os seus clientes móveis utilizando o fluxo direcionadas para o servidor:

* [Adicionar autenticação à sua aplicação iOS][iOS]
* [Adicionar autenticação à sua aplicação Android][Android]
* [Adicionar autenticação à sua aplicação do Windows][Windows]
* [Adicionar autenticação à sua aplicação xamarin. IOS][Xamarin. IOS]
* [Adicionar autenticação à sua aplicação xamarin. Android][xamarin. Android]
* [Adicionar autenticação à sua aplicação xamarin. Forms][xamarin. Forms]
* [Adicionar autenticação à sua aplicação Cordova][Cordova]

Se pretender utilizar o fluxo de cliente, direcionado para o Azure Active Directory, utilize os seguintes recursos:

* [Utilizar a biblioteca de autenticação do Active Directory para iOS][ADAL-iOS]
* [Utilize a biblioteca de autenticação do Active Directory para Android][ADAL-Android]
* [Utilizar a biblioteca de autenticação do Active Directory para o Windows e Xamarin][ADAL-dotnet]

Se pretender utilizar o fluxo de cliente, direcionado para o Facebook, utilize os seguintes recursos:

* [Utilizar o SDK do Facebook para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Se pretender utilizar o fluxo de cliente, direcionado para Twitter, utilize os seguintes recursos:

* [Utilizar recursos de infraestrutura do Twitter para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Se pretender utilizar o fluxo de cliente, direcionado para o Google, utilize os seguintes recursos:

* [Utilizar o SDK do início de sessão Google para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

<!-- ### API applications
The following tutorials show how to protect your API apps:

* [User authentication for API Apps in Azure App Service][apia-user]
* [Service principal authentication for API Apps in Azure App Service][apia-service] -->

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin. IOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[xamarin. Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[xamarin. Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
