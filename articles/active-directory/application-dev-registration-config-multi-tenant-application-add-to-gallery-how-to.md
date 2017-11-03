---
title: "Como adicionar uma aplicação de multi-inquilino para a Galeria de aplicações do Azure AD | Microsoft Docs"
description: "Explica como pode listar a aplicação multi-inquilino programada personalizada na Galeria de aplicações do Azure AD"
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
ms.openlocfilehash: 208f0d40bd7a8e8f35f16e1fcb09c305d833dbb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Como adicionar uma aplicação de multi-inquilino para a Galeria de aplicações do Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a Galeria de aplicações do Azure AD?

Galeria de aplicações do Azure AD é uma excelente forma de colocar a sua aplicação à frente de todos os milhões de clientes do Azure Active Directory para alargar o impacto e alcançar da sua aplicação no marketplace. Os passos abaixo explicam como pode listar a aplicação na Galeria de aplicações do Azure AD.

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Se a aplicação que suporta SAML ou OpenIDConnect
Se tiver uma aplicação de multi-inquilino que gostaria de lista na Galeria de aplicações do Azure AD, tem primeiro de se certificar de que a aplicação suporta um dos seguintes único início de sessão tecnologias:

1. **OpenID Connect** -integração direta com o Azure AD com OpenID Connect para autenticação e o Azure AD consentimento API para a configuração. Se estiver apenas a iniciar uma integração e a aplicação não suporta SAML, este é o modo de recomendamos.
2. **SAML** – a aplicação já tem a capacidade de configurar fornecedores de identidade de terceiros utilizando o protocolo SAML.

Se a aplicação suporta um destes modos de início de sessão único e pretende listar a aplicação multi-inquilino na Galeria de aplicações do Azure AD, pode seguir os passos no documento abaixo. Para começar a trabalhar rapidamente envie um e-mail para  **waadpartners@microsoft.com** .

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Se a aplicação não suporta SAML ou OpenIDConnect
Mesmo que a aplicação não suporta um dos modos destas, iremos ainda pode integrá-lo na nossa da galeria com a nossa tecnologia de palavra-passe-início de sessão único. Se pretende explorar esta opção, pode enviar um e-mail para  **waadpartners@microsoft.com** .

## <a name="next-steps"></a>Passos seguintes
[Como listar a aplicação na Galeria de aplicações do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
