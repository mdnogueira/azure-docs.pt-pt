---
title: "IOS de v2 do Azure AD introdução - introdução | Microsoft Docs"
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
ms.openlocfilehash: 948693c8501ecc46a1508e5ea085846d0910783e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="call-the-microsoft-graph-api-from-an-ios-app"></a>Chamar o Microsoft Graph API a partir de uma aplicação iOS

Este guia demonstra como uma aplicação nativa do iOS (Swift) pode obter um token de acesso e chamar outras APIs que necessitam de tokens de acesso a partir do ponto final de v2 do Azure Active Directory ou o Microsoft Graph API.

No final deste guia, a aplicação será possível chamar uma API protegida com contas pessoais (incluindo outlook.com, live.com e outros), bem como o trabalho contas escolares ou profissionais de qualquer da empresa ou organização que tenha o Azure Active Directory.

> ### <a name="pre-requisites"></a>Pré-requisitos
> - XCode 8. x é necessário para este guia. Pode transferir o XCode [aqui](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode transferir URL").
> - [Carthage](https://github.com/Carthage/Carthage) para gestão de pacotes

### <a name="how-this-guide-works"></a>Como funciona este guia

![Como funciona este guia](media/active-directory-mobileanddesktopapp-ios-introduction/iosintro.png)

A aplicação de exemplo criada por este guia ativa uma aplicação do iOS consultar a Graph API do Microsoft ou uma API Web que aceita tokens de ponto final do Azure Active Directory v2. Neste cenário, um token é adicionado a pedidos de HTTP através do cabeçalho de autorização. Aquisição do token e a renovação é processado pela biblioteca de autenticação do Microsoft (MSAL).


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Processamento de aquisição de token para aceder ao protegidos APIs da Web

Depois do utilizador é autenticado, a aplicação de exemplo recebe um token que pode ser utilizado para consultar a Graph API do Microsoft ou uma API Web protegida pelo Microsoft Azure Active Directory v2.

APIs, tais como o Microsoft Graph requerem um token de acesso para permitir o acesso a recursos específicos – por exemplo, um perfil de utilizador, calendário do utilizador de acesso de leitura ou enviar mensagens de correio eletrónico. A aplicação pode pedir um token de acesso utilizando MSAL especificando âmbitos de API. Este token de acesso é então adicionada ao cabeçalho de autorização de HTTP para cada chamada efetuada em relação ao recurso protegido.

MSAL gere a colocação em cache e atualizar os tokens de acesso para si, pelo que não tem da aplicação.


### <a name="nuget-packages"></a>Pacotes NuGet

Este guia utiliza os seguintes pacotes de NuGet:

|Biblioteca|Descrição|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Pré-visualização biblioteca de autenticação de Microsoft para iOS|

