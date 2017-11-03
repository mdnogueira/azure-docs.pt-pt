---
title: "Introdução ao Android v2 do Azure AD iniciada - teste | Microsoft Docs"
description: "Como uma aplicação Android pode obter um token de acesso e chamar Graph API do Microsoft ou de APIs que necessitam de tokens de acesso a partir do ponto final do Azure Active Directory v2"
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
ms.openlocfilehash: 6df64f4820f8409bd8897d5ac24f81bffeeef102
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
## <a name="test-your-code"></a>Testar o seu código

1. Implemente o código para o dispositivo/emulador.
2. Quando estiver pronto para testar, utilize (conta institucional) um Microsoft Azure Active Directory ou uma conta de Account Microsoft (live.com, outlook.com) para iniciar sessão. 

![Captura de ecrã de exemplo](media/active-directory-mobileanddesktopapp-android-test/mainwindow.png)
<br/><br/>
![Início de sessão](media/active-directory-mobileanddesktopapp-android-test/usernameandpassword.png)

### <a name="consent"></a>Consentimento
Na primeira vez que um utilizador inicia sessão sua aplicação, estes serão apresentados com um ecrã de consentimento semelhante para o abaixo, onde têm de aceitar explicitamente: 

![Consentimento](media/active-directory-mobileanddesktopapp-android-test/androidconsent.png)


### <a name="expected-results"></a>Resultados esperados
Deverá ver os resultados de uma chamada para o Microsoft Graph API 'me' ponto final utilizado para obter o perfil de utilizador - https://graph.microsoft.com/v1.0/me. Para obter uma lista de pontos finais de Microsoft Graph comuns, consulte este [artigo](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre âmbitos e as permissões delegadas

Requer o Microsoft Graph API do `user.read` âmbito para ler o perfil do utilizador. Este âmbito é adicionado automaticamente por predefinição em todas as aplicações que está a ser registada no nosso portal de registo. Algumas outras APIs para o Microsoft Graph, bem como APIs personalizadas para o servidor de back-end poderá exigir âmbitos adicionais. Por exemplo, para o Microsoft Graph, o âmbito `Calendars.Read` é necessário para a lista de calendários do utilizador. Para poder aceder ao calendário do utilizador num contexto de uma aplicação, terá de adicionar o `Calendars.Read` delegado permissão para informações de registo de aplicação e, em seguida, adicione o `Calendars.Read` âmbito para o `acquireTokenSilentAsync` chamada. Poderá ser pedido ao utilizador para consents adicionais como aumentar o número de âmbitos.

<!--end-collapse-->
