---
title: "Como selecionar permissões para uma determinado API | Microsoft Docs"
description: "Como localizar os pontos finais de autenticação para uma aplicação personalizada estiver a desenvolver ou registar com o Azure AD."
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 6966cf145375bf3d830d476564c428502ae40fd4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-select-permissions-for-a-given-api"></a>Como selecionar permissões para uma determinado API

Pode encontrar os pontos finais de autenticação para a sua aplicação no [portal do Azure](https://portal.azure.com).

-   Navegue para o [portal do Azure](https://portal.azure.com).

-   No painel de navegação esquerdo, clique em **do Azure Active Directory**.

-   Clique em **registos de aplicação** e escolha **pontos finais**.

-   Isto abrir o **pontos finais** página, que lista todos os pontos finais a autenticação para o seu inquilino.

-   Utilizar o ponto final específico para o protocolo de autenticação que está a utilizar, em conjunto com o ID de aplicação para craft a autenticação do pedido específicas da aplicação.

## <a name="next-steps"></a>Passos seguintes
[Guia para programadores do Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)
