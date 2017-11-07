---
title: "IOS de v2 do Azure AD introdução - teste | Microsoft Docs"
description: "Como as aplicações do iOS (Swift) podem chamar uma API que necessitam de tokens de acesso ao ponto final do Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: c002387c6d2c8ec83610398c337dc504e7253028
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Testar a consultar o Microsoft Graph API da sua aplicação iOS

Prima `Command`  +  `R` para executar o código no simulador do.

![Captura de ecrã de exemplo](media/active-directory-mobileanddesktopapp-ios-test/iostestscreenshot.png)

Quando estiver pronto para testar, toque em *'Chamar Microsoft Graph API'* e será solicitado para introduzir o nome de utilizador e palavra-passe.

### <a name="consent"></a>Consentimento
A primeira vez que iniciar sessão na sua aplicação, será apresentada com um ecrã de consentimento semelhante para o abaixo, onde tem de aceitar explicitamente:

![Ecrã de consentimento](media/active-directory-mobileanddesktopapp-ios-test/iosconsentscreen.png)

### <a name="expected-results"></a>Resultados esperados
Deverá ver informações do perfil de utilizador devolvidas pela Microsoft Graph API chamada o *registo* secção.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre âmbitos e as permissões delegadas

Requer o Microsoft Graph API do `user.read` âmbito para ler o perfil do utilizador. Este âmbito é adicionado automaticamente por predefinição em todas as aplicações que está a ser registada no nosso portal de registo. Algumas outras APIs para o Microsoft Graph, bem como APIs personalizadas para o servidor de back-end poderá exigir âmbitos adicionais. Por exemplo, para o Microsoft Graph, o âmbito `Calendars.Read` é necessário para a lista de calendários do utilizador. Para poder aceder ao calendário do utilizador num contexto de uma aplicação, terá de adicionar o `Calendars.Read` delegado permissão para informações de registo de aplicação e, em seguida, adicione o `Calendars.Read` âmbito para o `acquireTokenSilent` chamada. Poderá ser pedido ao utilizador para consents adicionais como aumentar o número de âmbitos.

<!--end-collapse-->

[!INCLUDE  [Help and Support Options](../../../../includes/active-directory-develop-help-support-include.md)]