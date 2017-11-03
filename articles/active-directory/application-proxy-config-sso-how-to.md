---
title: "Como configurar o início de sessão a uma aplicação de Proxy de aplicações | Microsoft Docs"
description: "Como pode configurar o início de sessão único para a aplicação de proxy de aplicação rapidamente"
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
ms.openlocfilehash: ccab427857b1439f37f3d9f193e35a4fc2237014
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Como configurar o início de sessão a uma aplicação de Proxy de aplicações

Início de sessão único (SSO) permite aos utilizadores aceder a uma aplicação sem autenticar várias vezes. Permite a autenticação única para ocorrer na nuvem, no Azure Active Directory e permite que o serviço ou o conector para representar o utilizador para concluir os desafios de autenticação adicionais da aplicação.

## <a name="how-to-configure-single-sign-on"></a>Como configurar o início de sessão único na
Para configurar o SSO, primeiro certifique-se de que a aplicação está configurada para a pré-autenticação através do Azure Active Directory. Para fazê-lo, aceda a **do Azure Active Directory**  - &gt; **aplicações empresariais**  - &gt; **todas as aplicações**  - &gt; a aplicação  **- &gt; Proxy de aplicações**. Nesta página, consulte o campo "Pré-autenticação" e certifique-se de que está definido para "do Azure Active Directory. 

Para obter mais informações sobre os métodos de pré-autenticação, consulte o quarto passo do [documento de publicação de aplicação](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal).

   ![Método de pré-autenticação no Portal do Azure](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Configurar os modos de início de sessão único para aplicações de Proxy de aplicações
Em seguida, configure o tipo específico de início de sessão único. Os métodos de início de sessão são classificados com base no tipo de autenticação que a aplicação de back-end utiliza. Aplicações de Proxy de aplicações suporta três tipos de início de sessão:

-   **Baseado em palavra-passe de início de sessão**: baseados em palavra-passe de início de sessão pode ser utilizado para qualquer aplicação que utiliza os campos de nome de utilizador e palavra-passe para início de sessão. Passos de configuração podem ser encontrados na nossa [documentação de configuração de palavra-passe SSO](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal#bring-your-own-password-sso-applications).

-   **Autenticação integrada do Windows**: para aplicações utilizando a autenticação integrada de Windows (IWA), o início de sessão único está ativado através do delegação de restrita de Kerberos (KCD). Isto dá-permissão de conectores do Proxy de aplicações no Active Directory para representar os utilizadores e para enviar e receber tokens em nome daqueles. Podem ser encontrados detalhes sobre como configurar o KCD no [-início de sessão único com documentação KCD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd).

-   **Com base no cabeçalho de início de sessão**: com base no cabeçalho de início de sessão é ativado através de uma parceria e requer configuração adicional. Para obter detalhes sobre o parceria e instruções passo a passo para configurar o início de sessão para uma aplicação que utiliza os cabeçalhos de autenticação, consulte o [PingAccess para obter a documentação do Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access).

Cada uma destas opções pode ser encontrada ao aceder à sua aplicação em "As aplicações empresariais" e abrir o **Single Sign-On** página no menu da esquerda. tenha em atenção que, se a aplicação foi criada no portal do antigo, não poderá ver todas estas opções.

Nesta página, também verá uma opção de início de sessão adicional: o início de sessão ligado. Isto também é suportado pelo Proxy da aplicação. No entanto, tenha em atenção que esta opção não adiciona o início de sessão único para a aplicação. Dito isto que a aplicação pode já ter o início de sessão implementada utilizando o outro serviço, tais como serviços de Federação do Active Directory. 

Esta opção permite que um administrador para criar uma ligação a uma aplicação que telefone primeiro dos utilizadores no quando o acesso à aplicação. Por exemplo, se existir uma aplicação que está configurada para autenticar utilizadores que utilizam 2.0 de serviços de Federação do Active Directory, um administrador pode utilizar a opção "ligado início de sessão" para criar uma ligação ao mesmo no painel de acesso.

## <a name="next-steps"></a>Passos seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy da aplicação](active-directory-application-proxy-sso-using-kcd.md)
