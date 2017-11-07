---
title: "Azure AD v2 JS SPA orientado configuração - teste | Microsoft Docs"
description: "Como aplicações de JavaScript SPA podem chamar uma API que necessitam de tokens de acesso ao ponto final do Azure Active Directory v2"
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
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: e10e5bbb035878eb62d9295b91263602f8128230
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
## <a name="test-your-code"></a>Testar o seu código

> ### <a name="testing-with-visual-studio"></a>Testar com o Visual Studio
> Se estiver a utilizar o Visual Studio, prima `F5` para executar o projeto: o browser abre e direciona a *http://localhost: {porta}* onde vir o *chamar Microsoft Graph API* botão.

<p/><!-- -->

> ### <a name="testing-with-python-or-another-web-server"></a>Testar com o Python ou outro servidor web
> Se não estiver a utilizar o Visual Studio, certifique-se de que o servidor web é iniciado e está configurado para escutar uma porta TCP com base na pasta que contém o *index.html* ficheiro. Para o Python, pode começar a escutar a porta ao executar o comando prompt / terminal, a partir da pasta da aplicação:
> 
> ```bash
> python -m http.server 8080
> ```
>  Em seguida, abra o browser e o tipo *http://localhost:8080* ou *http://localhost: {porta}* - onde o *porta* corresponde à porta que o servidor web está a escutar. Deverá ver o conteúdo da página index.html com o *chamar Microsoft Graph API* botão.

## <a name="test-your-application"></a>Testar a aplicação

Depois do browser carrega o *index.html*, clique em de *chamar Microsoft Graph API* botão. Se esta for a primeira vez, o browser redireciona-o para o Microsoft Azure Active Directory v2 ponto final, onde lhe for pedido para iniciar sessão.
 
![Captura de ecrã de exemplo](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>Consentimento
A muito primeira vez que iniciar sessão na sua aplicação, é-lhe apresentado um ecrã de consentimento semelhante ao seguinte, onde tem de aceitar:

 ![Ecrã de consentimento](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)


### <a name="expected-results"></a>Resultados esperados
Deverá ver informações de perfil de utilizador devolvidas pela resposta de chamada Microsoft Graph API.
 
 ![Resultados](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

Consulte também informações básicas sobre o token adquirido no *Token de acesso* e *afirmações de Token de ID* caixas.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre âmbitos e as permissões delegadas

Requer o Microsoft Graph API do `user.read` âmbito para ler o perfil do utilizador. Este âmbito é adicionado automaticamente por predefinição em todas as aplicações que está a ser registada no nosso portal de registo. Algumas outras APIs para o Microsoft Graph, bem como APIs personalizadas para o servidor de back-end poderá exigir âmbitos adicionais. Por exemplo, para o Microsoft Graph, o âmbito `Calendars.Read` é necessário para a lista de calendários do utilizador. Para poder aceder ao calendário do utilizador no contexto de uma aplicação, terá de adicionar o `Calendars.Read` delegado permissão para informações de registo de aplicação e, em seguida, adicione o `Calendars.Read` âmbito para o `acquireTokenSilent` chamada. Poderá ser pedido ao utilizador para consents adicionais como aumentar o número de âmbitos.

Se um API de back-end não necessita de um âmbito (não recomendado), pode utilizar o `clientId` como o âmbito no `acquireTokenSilent` e/ou `acquireTokenRedirect` chamadas.

<!--end-collapse-->

[!INCLUDE  [Help and Support Options](../../../../includes/active-directory-develop-help-support-include.md)]
