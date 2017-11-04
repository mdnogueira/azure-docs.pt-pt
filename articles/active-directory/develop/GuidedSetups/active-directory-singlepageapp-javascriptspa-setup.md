---
title: "Azure AD v2 JS SPA orientado configuração - configuração | Microsoft Docs"
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
ms.openlocfilehash: fc9f88cc8d23abcfa8ea30e346192732b422ffa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
## <a name="setting-up-your-web-server-or-project"></a>Configurar o seu servidor web ou o projeto

> Prefere transferir o projeto este exemplo em vez disso? 
> - [Transferir o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> ou
> - [Transferir os ficheiros de projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip) para um servidor local web, como o Python
>
> E, em seguida, avance para o [passo da configuração](#create-an-application-express) para configurar o exemplo de código antes de executá-lo.

## <a name="prerequisites"></a>Pré-requisitos
Um servidor local web como [Python http.server](https://www.python.org/downloads/), [servidor http](https://www.npmjs.com/package/http-server/), [.NET Core](https://www.microsoft.com/net/core), ou IIS Express integração com [Visual Studio 2017](https://www.visualstudio.com/downloads/) é necessário para executar este programa de configuração orientado. 

As instruções neste guia baseiam-se no Python e Visual Studio 2017, mas pode utilizar qualquer outro ambiente de desenvolvimento ou servidor Web.

## <a name="create-your-project"></a>Criar o projeto 

> ### <a name="option-1-visual-studio"></a>Opção 1: Visual Studio 
> Se estiver a utilizar o Visual Studio e estiver a criar um novo projeto, siga os passos abaixo para criar uma nova solução do Visual Studio:
> 1.    No Visual Studio:`File` > `New` > `Project`
> 2.    Em `Visual C#\Web`, selecione`ASP.NET Web Application (.NET Framework)`
> 3.    Nome da aplicação e clique em *OK*
> 4.    Em `New ASP.NET Web Application`, selecione`Empty`

<p/><!-- -->

> ### <a name="option-2-python-other-web-servers"></a>Opção 2: Python / outros servidores web
> Certifique-se de que instalou [Python](https://www.python.org/downloads/), em seguida, siga o passo abaixo:
> - Crie uma pasta para alojar a aplicação.


## <a name="create-your-single-page-applications-ui"></a>Criar a IU sua aplicação única página
1.  Criar um *index.html* ficheiro para o seu SPA JavaScript. Se estiver a utilizar o Visual Studio, selecione o projeto (pasta raiz do projeto), clique com o botão direito e selecione: `Add`  >  `New Item`  >  `HTML page` e dê-lhe o nome index.html
2.  Adicione o seguinte código à sua página:
```html
<!DOCTYPE html>
<html>
<head>
    <!-- bootstrap reference used for styling the page -->
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <title>JavaScript SPA Guided Setup</title>
</head>
<body style="margin: 40px">
    <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphApi()">Call Microsoft Graph API</button>
    <div id="errorMessage" class="text-danger"></div>
    <div class="hidden">
        <h3>Graph API Call Response</h3>
        <pre class="well" id="graphResponse"></pre>
    </div>
    <div class="hidden">
        <h3>Access Token</h3>
        <pre class="well" id="accessToken"></pre>
    </div>
    <div class="hidden">
        <h3>ID Token Claims</h3>
        <pre class="well" id="userInfo"></pre>
    </div>
    <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

    <!-- This app uses cdn to reference msal.js (recommended). 
         You can also download it from: https://github.com/AzureAD/microsoft-authentication-library-for-js -->
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.1.1/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````
