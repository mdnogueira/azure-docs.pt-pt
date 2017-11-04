---
title: "Introdução ao Android v2 do Azure AD iniciada - introdução | Microsoft Docs"
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
ms.openlocfilehash: d933781713456f73aa76db557fdf35672dfb2a29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="call-the-microsoft-graph-api-from-an-android-app"></a>Chamar o Microsoft Graph API a partir de uma aplicação Android

Este guia demonstra como uma aplicação Android nativa pode obter um token de acesso e chamar outras APIs que necessitam de tokens de acesso a partir do ponto final do Azure Active Directory v2 ou Microsoft Graph API.

No final deste guia, a aplicação será possível chamar uma API protegida com contas pessoais (incluindo outlook.com, live.com e outros), bem como o trabalho contas escolares ou profissionais de qualquer da empresa ou organização que tenha o Azure Active Directory.  

### <a name="how-this-sample-works"></a>Como funciona este exemplo
![Como funciona este exemplo](media/active-directory-mobileanddesktopapp-android-intro/android-intro.png)

O exemplo criado por este guia baseia-se um cenário onde uma aplicação Android é utilizada para consultar uma API Web que aceita tokens a partir do Azure Active Directory v2 endpoint – neste caso, Microsoft Graph API. Neste cenário, um token é adicionado a pedidos de HTTP através do cabeçalho de autorização. Aquisição do token e a renovação é processado pela biblioteca de autenticação do Microsoft (MSAL).

### <a name="pre-requisites"></a>Pré-requisitos
* Esta configuração orientada concentra-se no Android Studio, mas qualquer outro ambiente de desenvolvimento de uma aplicação Android também é aceitável. 
* É necessário o Android SDK 21 ou mais recente (recomenda-se SDK 25).
* Google Chrome ou um browser utilizando separadores personalizada é necessário para esta versão da biblioteca de autenticação da Microsoft (MSAL) para Android.

> Nota: Google Chrome não está incluído no Visual Studio emulador do Android. Recomendamos que para testar este código um emulador com API 25 ou uma imagem com API 21 ou mais recente que tem com o Google Chrome instalada.


### <a name="how-to-handle-token-acquisition-to-access-a-protected-web-api"></a>Como processar a aquisição de token para aceder a uma API Web protegida

Depois do utilizador é autenticado, a aplicação de exemplo recebe um token que pode ser utilizado para consultar o Microsoft Graph API ou uma API Web protegida pelo Microsoft Azure Active Directory v2.

APIs, tais como o Microsoft Graph requerem um token de acesso para permitir o acesso a recursos específicos – por exemplo, um perfil de utilizador, calendário do utilizador de acesso de leitura ou enviar mensagens de correio eletrónico. A aplicação pode pedir um token de acesso utilizando MSAL para aceder a estes recursos, especificando os âmbitos de API. Este token de acesso é então adicionada ao cabeçalho de autorização de HTTP para cada chamada efetuada em relação ao recurso protegido. 

MSAL gere a colocação em cache e atualizar os tokens de acesso para si, pelo que não tem da aplicação.

### <a name="libraries"></a>Bibliotecas

Este guia utiliza as bibliotecas seguintes:

|Biblioteca|Descrição|
|---|---|
|[com.microsoft.Identity.Client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Biblioteca de autenticação da Microsoft (MSAL)|
