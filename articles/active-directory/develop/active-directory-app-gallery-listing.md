---
title: "Listar a aplicação na Galeria de aplicações do Azure Active Directory"
description: "Como listar uma aplicação que suporta o início de sessão único na galeria do Azure Active Directory | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 1d315cf63bcbf37b6b03b5a965ac615282526682
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Listar a aplicação na Galeria de aplicações do Azure Active Directory
Lista de uma aplicação que suporta o início de sessão com o Azure Active Directory no [galeria do Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/), a aplicação primeiro tem de implementar um dos seguintes modos de integração:

* **OpenID Connect** -integração direta com o Azure AD com OpenID Connect para autenticação e o Azure AD consentimento API para a configuração. Se estiver apenas a iniciar uma integração e a aplicação não suporta SAML, este é o modo de recomendamos.
* **SAML** – a aplicação já tem a capacidade de configurar fornecedores de identidade de terceiros utilizando o protocolo SAML.

Requisitos de listagem para cada modo são abaixo.

## <a name="openid-connect-integration"></a>OpenID Connect integração
Para integrar a sua aplicação com o Azure AD, seguindo o [instruções de programador](active-directory-authentication-scenarios.md). Em seguida, conclua as perguntas abaixo e enviar para waadpartners@microsoft.com.

* Forneça credenciais para uma conta de inquilino de teste ou com a sua aplicação que pode ser utilizada pela equipa do Azure AD para testar a integração.  
* Fornece instruções sobre como a equipa do Azure AD pode iniciar sessão e ligar uma instância do Azure AD para a sua aplicação utilizando o [framework de consentimento do Azure AD](active-directory-integrating-applications.md#overview-of-the-consent-framework). 
* Forneça quaisquer instruções adicionais necessárias para a equipa do Azure AD para início de sessão com a sua aplicação de teste. 
* Forneça as informações abaixo:

> Nome da empresa:
> 
> Web site da empresa:
> 
> Nome da aplicação:
> 
> Descrição da aplicação (limite de caracteres 200):
> 
> O site de aplicação (informativa):
> 
> Site de suporte técnico a nível de aplicação ou informações de contacto:
> 
> ID da aplicação, conforme mostrado nos detalhes da aplicação em https://portal.azure.com:
> 
> Aplicação Sign-Up URL onde os clientes aceda ao inscrever-se e /or comprar a aplicação:
> 
> Selecione até três categorias para a sua aplicação estar listado abaixo (para categorias disponíveis, consulte o Azure Active Directory Marketplace):
> 
> Ligar a aplicação ícone pequeno (ficheiro PNG, 45px por 45px, a cor de fundo sólida):
> 
> Ligar a aplicação de ícones grandes (ficheiro PNG, 215px por 215px, a cor de fundo sólida):
> 
> Anexe o logótipo da aplicação (ficheiro PNG, 150px por 122px, a cor de fundo transparente):
> 
> 

## <a name="saml-integration"></a>Integração de SAML
Qualquer aplicação que suporta SAML 2.0 pode ser integrada diretamente com um inquilino do Azure AD utilizando [estas instruções para adicionar uma aplicação personalizada](../application-config-sso-how-to-configure-federated-sso-non-gallery.md). Assim que tiver testado que a integração de aplicações funciona com o Azure AD, enviar as informações seguintes para < mailto:waadpartners@microsoft.com >.

* Forneça credenciais para uma conta de inquilino de teste ou com a sua aplicação que pode ser utilizada pela equipa do Azure AD para testar a integração.  
* Forneça o URL de início de sessão SAML, URL de emissor (ID de entidade) e o URL de resposta (serviço de consumidor asserção) valores para a sua aplicação, conforme descrito [aqui](../application-config-sso-how-to-configure-federated-sso-non-gallery.md). Se, normalmente, forneça estes valores como parte de um ficheiro de metadados SAML, em seguida, envie que bem.
* Forneça uma breve descrição de como configurar o Azure AD como um fornecedor de identidade na sua aplicação utilizando SAML 2.0. Se a aplicação suportar a configuração do Azure AD como um fornecedor de identidade através de um portal self-service administrativo, em seguida, certifique-se as credenciais fornecidas acima incluem a capacidade de configurar esta opção.
* Forneça as informações abaixo:

> Nome da empresa:
> 
> Web site da empresa:
> 
> Nome da aplicação:
> 
> Descrição da aplicação (limite de caracteres 200):
> 
> O site de aplicação (informativa):
> 
> Site de suporte técnico a nível de aplicação ou informações de contacto:
> 
> Aplicação Sign-Up URL onde os clientes aceda ao inscrever-se e /or comprar a aplicação:
> 
> Escolha até três categorias para a sua aplicação estar listado abaixo (para categorias disponíveis, consulte o [Azure Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/))):
> 
> Ligar a aplicação ícone pequeno (ficheiro PNG, 45px por 45px, a cor de fundo sólida):
> 
> Ligar a aplicação de ícones grandes (ficheiro PNG, 215px por 215px, a cor de fundo sólida):
> 
> Anexe o logótipo da aplicação (ficheiro PNG, 150px por 122px, a cor de fundo transparente):
> 
> 

