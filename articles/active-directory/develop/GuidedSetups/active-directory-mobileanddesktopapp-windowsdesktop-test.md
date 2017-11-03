---
title: Azure AD v2 Windows ambiente de trabalho ao obter iniciada - teste | Microsoft Docs
description: "Como aplicações de .NET de ambiente de trabalho do Windows (XAML) podem chamar uma API que necessitam de tokens de acesso ao ponto final do Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 972cc48057c13271d725b0c973c3ccf651ad27c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
## <a name="test-your-code"></a>Testar o seu código

Para testar a aplicação, prima `F5` para executar o projeto no Visual Studio. Deverá aparecer a janela principal:

![Captura de ecrã de exemplo](media/active-directory-mobileanddesktopapp-windowsdesktop-test/samplescreenshot.png)

Quando estiver pronto para testar, clique em *chamar Microsoft Graph API* e utilizar (conta institucional) um Microsoft Azure Active Directory ou uma conta de Account Microsoft (live.com, outlook.com) para iniciar sessão. Esta é a primeira vez, verá uma janela solicitando utilizador iniciar sessão:

![Início de sessão](media/active-directory-mobileanddesktopapp-windowsdesktop-test/signinscreenshot.png)

### <a name="consent"></a>Consentimento
A primeira vez que iniciar sessão na sua aplicação, será apresentada com um ecrã de consentimento semelhante para o abaixo, onde tem de aceitar explicitamente:

![Ecrã de consentimento](media/active-directory-mobileanddesktopapp-windowsdesktop-test/consentscreen.png)

### <a name="expected-results"></a>Resultados esperados
Deverá ver informações de perfil de utilizador devolvidas pela chamada Microsoft Graph API no ecrã de resultados de chamada de API.

Também deverá ver informações básicas sobre o token adquirido através de `AcquireTokenAsync` ou `AcquireTokenSilentAsync` na caixa de informações de Token:

|Propriedade  |formato  |Descrição |
|---------|---------|---------|
|Nome | {Nome completo do utilizador} |O utilizador do primeiro e último nome|
|Nome de utilizador |<span>user@domain.com</span> |O nome de utilizador utilizado para identificar o utilizador|
|Token expira |{DateTime}         |O tempo no qual o token expira. MSAL irá expandir a data de expiração para si renovando o token quando necessário|
|Token de acesso |{Cadeia}         |A cadeia do token enviados que serão enviadas para pedidos HTTP que necessitem de um cabeçalho de autorização|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre âmbitos e as permissões delegadas
Graph API requer o `user.read` âmbito para ler o perfil de utilizador. Este âmbito é adicionado automaticamente por predefinição em todas as aplicações que está a ser registada no nosso portal de registo. Algumas outras APIs de gráfico, bem como APIs personalizadas para o servidor de back-end requerem âmbitos adicionais. Por exemplo, para gráfico, `Calendars.Read` é necessário aos calendários do utilizador da lista. Para poder aceder ao calendário do utilizador num contexto de uma aplicação, terá de adicionar `Calendars.Read` delegados informações de registo de aplicação e, em seguida, adicionar `Calendars.Read` para o `AcquireTokenAsync` chamada. Poderá ser solicitado ao utilizador consents adicionais como aumentar o número de âmbitos.

<!--end-collapse-->



