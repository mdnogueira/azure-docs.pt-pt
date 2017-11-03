---
title: "Início de sessão único para aplicações com o Proxy de aplicações do Azure AD | Microsoft Docs"
description: "Ative único início de sessão para as suas aplicações publicadas no local com o Proxy de aplicações do Azure AD no portal do Azure."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 9ddc0c1bd5f2cbb24f6761cfd041b820ee6464b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Palavra-passe vaulting para início de sessão com o Proxy da aplicação

Proxy de aplicações do Active Directory do Azure ajuda a melhorar a produtividade através da publicação de aplicações no local para que os empregados remotos podem aceder de forma segura, demasiado. No portal do Azure, pode também configurar início de sessão único (SSO) para estas aplicações. Os utilizadores só precisam para autenticar com o Azure AD e podem aceder à aplicação empresarial sem terem de voltar a iniciar sessão.

Proxy de aplicações suporta vários [único modos de início de sessão](application-proxy-sso-overview.md). Baseado em palavra-passe de início de sessão destina-se para as aplicações que utilizam uma combinação de nome de utilizador/palavra-passe para autenticação. Quando configura baseada em palavra-passe de início de sessão para a sua aplicação, os utilizadores têm de iniciar sessão na aplicação no local, uma vez. Depois disso, o Azure Active Directory armazena as informações de início de sessão e automaticamente fornece-o para a aplicação quando os utilizadores acedem remotamente. 

Já deverá ter publicado e testado a sua aplicação com o Proxy de aplicações. Se não, siga os passos no [publicar aplicações através do Proxy de aplicações do Azure AD](application-proxy-publish-azure-portal.md) , em seguida, volte aqui. 

## <a name="set-up-password-vaulting-for-your-application"></a>Configurar a palavra-passe vaulting para a sua aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Selecione **do Azure Active Directory** > **aplicações empresariais** > **todas as aplicações**.
3. Na lista, selecione a aplicação que pretende configurar com SSO.  
4. Selecione **de sessão único-**.

   ![Selecione o início de sessão único](./media/application-proxy-sso-azure-portal/select-sso.png)

5. Para o modo SSO, escolha **baseada em palavra-passe de início de sessão**.
6. Para o URL de início de sessão, introduza o URL para a página onde os utilizadores introduzem o respetivo nome de utilizador e palavra-passe para iniciar sessão na sua aplicação fora da rede empresarial. Isto poderá ser o URL externo que criou quando publicado a aplicação através do Proxy de aplicações. 

   ![Escolher com base em palavra-passe de início de sessão e introduza o URL](./media/application-proxy-sso-azure-portal/password-sso.png)

7. Selecione **Guardar**.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testar a aplicação

Aceda ao URL externo que configurou para o acesso remoto à sua aplicação. Inicie sessão com as suas credenciais para essa aplicação (ou as credenciais para uma conta de teste que configurou com acesso). Depois de iniciar sessão com êxito, deverá conseguir deixar a aplicação e voltar atrás sem introduzir as suas credenciais novamente. 

## <a name="next-steps"></a>Passos seguintes

- Leia sobre outras formas de implementar [-início de sessão único com o Proxy da aplicação](application-proxy-sso-overview.md)
- Saiba mais sobre [considerações de segurança para aceder a aplicações remotamente com o Proxy de aplicações do Azure AD](application-proxy-security-considerations.md)