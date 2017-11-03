---
title: "Problemas em iniciar sessão a uma aplicação desenvolvida personalizado | Microsoft Docs"
description: "Rrors comuns que pode estar a causar a não conseguir iniciar sessão numa aplicação ter desenvolvidas com o Azure AD"
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
ms.openlocfilehash: b0df23e040a73d18968f547eef7347f14cc577c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="problems-signing-in-to-an-custom-developed-application"></a>Problemas em iniciar sessão a uma aplicação desenvolvida personalizada

Existem vários erros que pode estar a causar a não conseguir iniciar sessão numa aplicação. O maior razão pessoas ocorrer que este problema é configurado incorretamente aplicações.

## <a name="errors-related-to--misconfigured-apps"></a>Erros relacionados com a configuração incorreta aplicações

* Certifique-se de que ambas as configurações no portal de corresponder ao que tem na sua aplicação. Especificamente, compare o ID de cliente/aplicação, URLs de resposta, os segredos/chaves de cliente e URI de ID de aplicação.

* Comparar o recurso a pedir acesso no código com as permissões configuradas no **necessários recursos** separador para se certificar de que apenas solicita recursos que configurou.

* Consulte [do Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory) para quaisquer problemas ou sobre erros semelhantes.

## <a name="next-steps"></a>Passos seguintes

[Guia do programador do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Consentimento e integrar aplicações com o Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications>)<br>

[Aplicações de convergência consentimento e Permissioning para v 2.0 do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory>)
