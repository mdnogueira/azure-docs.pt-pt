---
title: "Pedido de consentimento inesperado quando iniciar sessão a uma aplicação | Microsoft Docs"
description: "Como resolver problemas quando um utilizador verá um pedido de consentimento para uma aplicação que tem de ser integrado com o Azure AD que não era esperado"
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
ms.openlocfilehash: e5b823e1251a7221f73efe6838d439f827f9665d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Pedido de consentimento inesperado quando iniciar sessão a uma aplicação

Muitas aplicações que se integram com o Azure Active Directory requerem permissões a vários recursos para executar. Quando estes recursos também estão integrados no Azure Active Directory, as permissões para aceder-lhes é pedido utilizando a estrutura de consentimento do Azure AD. 

Isto resulta num consentimento linha de comandos que está a ser apresentado na primeira vez que é utilizada uma aplicação, que é, muitas vezes, uma operação única. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Cenários em que os utilizadores veem consentimento dos avisos

Avisos adicionais podem ser esperados em vários cenários:

* O conjunto de permissões necessárias para a aplicação ter sido alterado.

* O utilizador que originalmente autorizado a aplicação não era um administrador e agora um utilizador diferente (não-administrador) está a utilizar a aplicação pela primeira vez.

* O utilizador que originalmente autorizado a aplicação foi um administrador, mas não foi possível consentimento em nome de toda a organização.

* A aplicação está a utilizar [consentimento incremental e dinâmico](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) para solicitar permissões adicionais depois de consentimento inicialmente foi concedido. Isto é frequentemente utilizado quando funcionalidades opcionais de uma aplicação adicional requerem permissões para além daquelas necessário para a funcionalidade de linha de base.

* Consentimento foi revogado depois de ser concedido inicialmente.

* O programador configurou a aplicação para exigir um pedido de consentimento sempre que é utilizada (Nota: não é recomendado).

## <a name="next-steps"></a>Passos seguintes

-   [As aplicações, permissões e consentimento no Azure Active Directory (v 1.0 ponto final)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Âmbitos, permissões e consentimento no Azure Active Directory (ponto final v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


