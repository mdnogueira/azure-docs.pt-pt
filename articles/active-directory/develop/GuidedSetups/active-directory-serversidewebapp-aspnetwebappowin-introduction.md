---
title: "Azure AD v2 ASP.NET Web Server obter iniciada - introdução | Microsoft Docs"
description: "Implementar Microsoft início de sessão numa solução ASP.NET com uma aplicação de baseadas no browser web tradicional utilizando o padrão de OpenID Connect"
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
ms.openlocfilehash: 8062923b6270ec6253dc231a3db4333cf4666b42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Adicionar início de sessão com a Microsoft para uma aplicação web ASP.NET

Este guia demonstra como implementar o início de sessão com a Microsoft através de uma solução de ASP.NET MVC com uma aplicação de baseada no browser tradicional web com OpenID Connect. 

No final deste guia, a aplicação será capaz de aceitar o início de sessão ins do pessoal contas (incluindo outlook.com, live.com e outros), bem como funcionam contas escolares ou profissionais de qualquer da empresa ou organização que tem integrado com o Azure Active Directory. 

> Este guia requer o Visual Studio 2015 Update 3 ou Visual Studio 2017.  Não o tiver?  [Transferir o Visual Studio 2017 gratuitamente](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>Como funciona este guia

![Como funciona este guia](media/active-directory-serversidewebapp-aspnetwebappowin-intro/aspnetbrowsergeneral.png)

Este guia baseia-se no cenário onde um browser acede a um web site do ASP.NET, pedir um utilizador para autenticar através de um botão de início de sessão. Neste cenário, a maioria do trabalho para compor a página web ocorre no lado do servidor.

## <a name="libraries"></a>Bibliotecas

Este guia utiliza as bibliotecas seguintes:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware ativa uma aplicação a utilizar OpenIdConnect para autenticação|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware ativa uma aplicação manter a utilização de cookies de sessão de utilizador|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Permite que as aplicações com base em OWIN são executadas no IIS através do pipeline de pedido do ASP.NET|

