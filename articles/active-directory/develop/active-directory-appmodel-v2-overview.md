---
title: Ponto final de v 2.0 do Azure Active Directory | Microsoft Docs
description: "Uma introdução à criação de aplicações com o Azure Active Directory e Account Microsoft início de sessão."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: a6a7c6bdf3deaee3a3949fe409a7fab6b7664695
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Início de sessão Microsoft Account & utilizadores do Azure AD numa única aplicação
No passado, um programador de aplicações que pretendiam para suportar ambas as contas pessoais da Microsoft e funcionam contas do Azure Active Directory foi necessários para integrar com dois sistemas separados.  O **ponto final de v 2.0 do Azure AD** introduz uma nova versão de API de autenticação permite-lhe iniciar sessão em ambos os tipos de contas utilizando uma integração simple.  As aplicações que utilizem o ponto final v 2.0 também podem consumir APIs REST do [Microsoft Graph](https://graph.microsoft.io) utilizando qualquer tipo de conta.

## <a name="getting-started"></a>Introdução
Escolha a sua plataforma favorita na lista seguinte para criar uma aplicação utilizando as nossas bibliotecas de open source para & estruturas.  Em alternativa, pode utilizar a nossa documentação do protocolo OAuth 2.0 & OpenID Connect para enviar e receber mensagens de protocolo diretamente, sem utilizar uma biblioteca de autenticação.

<br />

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>Novidades
As informações aqui será útil compreender o que é & o que não é possível com o ponto final v 2.0.

* Saiba mais sobre o [tipos de aplicações, pode criar com o ponto final v 2.0](active-directory-v2-flows.md).
* Compreender o [limitações e restrições](active-directory-v2-limitations.md) com o ponto final v 2.0.
* Veja esta descrição geral do vídeo para o ponto final v 2.0:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="reference"></a>Referência
Estas hiperligações serão úteis para explorar a plataforma em profundidade:

* [Referência do protocolo de v 2.0](active-directory-v2-protocols.md)
* [Referência de Token de v 2.0](active-directory-v2-tokens.md)
* [Referência da biblioteca de v 2.0](active-directory-v2-libraries.md)
* [Âmbitos e consentimento no ponto final v 2.0](active-directory-v2-scopes.md)
* [O Microsoft Graph](https://graph.microsoft.io)

> [!NOTE]
> Se só precisa de iniciar sessão em contas profissionais e escolares do Azure Active Directory, deve começar com o nosso [guia para programadores do Azure AD](active-directory-developers-guide.md).  O ponto final v 2.0 destina-se a programadores que necessitem explicitamente para iniciar sessão em contas pessoais da Microsoft.


[!INCLUDE  [Help and Support Options](../../../includes/active-directory-develop-help-support-include.md)]
