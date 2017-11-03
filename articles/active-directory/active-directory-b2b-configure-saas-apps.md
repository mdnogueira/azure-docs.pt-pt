---
title: "Configurar aplicações SaaS colaboração B2B do Azure Active Directory | Microsoft Docs"
description: "Exemplos de código e o PowerShell para colaboração B2B do Azure Active Directory do"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: 149a493f7b369415f0a2726dd6a576f0195c13d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Configurar aplicações SaaS colaboração B2B

Colaboração B2B do Active Directory (Azure AD) Azure funciona com a maioria das aplicações que se integram com o Azure AD. Nesta secção, iremos guiá-lo através de instruções para configurar algumas aplicações de SaaS populares para utilização com o Azure AD B2B.

Antes de observar instruções específicas da aplicação, seguem-se algumas regras geral:

* Para a maioria das aplicações, a configuração do utilizador tem de acontecer manualmente. Ou seja, os utilizadores têm de ser criados manualmente, bem como a aplicação.

* Para aplicações que suportam a configuração automática, tais como o Dropbox, convites separados criados a partir de aplicações. Os utilizadores devem ser se esqueça de aceitar cada convite.

* Nos atributos de utilizador, para mitigar os problemas com o disco de perfil de utilizador mangled (UDP) em utilizadores convidados, sempre definido **identificador de utilizador** para **user.mail**.


## <a name="dropbox-business"></a>Dropbox de negócio

Para permitir aos utilizadores iniciar sessão utilizando a respetiva conta de organização, tem de configurar manualmente o negócio Dropbox para utilizar o Azure AD como um fornecedor de identidade de Security Assertion Markup Language (SAML). Se não tiver sido configurada Dropbox empresariais para o fazer, não é possível pedir ou caso contrário, permitir aos utilizadores iniciar sessão em utilizar o Azure AD.

1. Para adicionar a aplicação de negócio Dropbox com o Azure AD, selecione **aplicações empresariais** no painel esquerdo e, em seguida, clique em **adicionar**.

  ![O botão "Adicionar" na página de aplicações da empresa](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

2. No **adicionar uma aplicação** janela, introduza **dropbox** na caixa de pesquisa e, em seguida, selecione **Dropbox para empresas** na lista de resultados.

  ![Procure "dropbox" em Adicionar uma página da aplicação](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

3. No **de sessão único-** página, selecione **de sessão único-** no painel esquerdo e, em seguida, introduza **user.mail** no **identificador de utilizador** caixa. (Está definido como UPN por predefinição.)

  ![Configurar o início de sessão para a aplicação](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

4. Para transferir o certificado a utilizar para a configuração da Dropbox, selecione **configurar DropBox**e, em seguida, selecione **único início de sessão no URL do serviço SAML** na lista.

  ![Transferir o certificado para a configuração da Dropbox](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. Inicie sessão no Dropbox com o URL de início de sessão do **de sessão único-** página.

  ![A página de início de sessão Dropbox](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

6. No menu, selecione **consola de administração**.

  ![A hiperligação "Consola de administração" no Dropbox menu](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

7. No **autenticação** caixa de diálogo, selecione **mais**, carregue o certificado e, em seguida, no **iniciar sessão no URL** box, introduza o SAML URL single sign-on.

  ![A hiperligação "Mais" na caixa de diálogo de autenticação fechada](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![O "iniciar sessão no URL" na caixa de diálogo de autenticação expandida](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

8. Para configurar o programa de configuração automática do utilizador no portal do Azure, selecione **aprovisionamento** no painel esquerdo, selecione **automática** no **modo de aprovisionamento** caixa e, em seguida, selecione **autorizar**.

  ![Configurar o aprovisionamento de utilizadores automática no portal do Azure](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

Depois dos utilizadores convidados ou membro tiverem sido configurados na aplicação da Dropbox, estes recebem um convite separada da Dropbox. Para utilizar o Dropbox-início de sessão único, os convidados precisam tem de aceitar o convite de clicar numa hiperligação no mesmo.

## <a name="box"></a>Box
Pode ativar a utilizadores autenticar os utilizadores convidados de caixa com a conta do Azure AD através da utilização de federação que é baseada no protocolo SAML. Neste procedimento, tem de carregar metadados para Box.com.

1. Adicione a aplicação de caixa das aplicações da empresa.

2. Configure o início de sessão único pela seguinte ordem:

  ![Configurar caixa-início de sessão único](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

 a. No **iniciar sessão no URL** caixa, certifique-se de que o URL de início de sessão é definido adequadamente para a caixa no portal do Azure. Este URL é o URL do seu inquilino Box.com. A Convenção de nomenclatura deve seguir *https://.box.com*.  
 O **identificador** não é aplicável a esta aplicação, mas ainda é apresentado como um campo obrigatório.

 b. No **identificador de utilizador** box, introduza **user.mail** (para SSO para as contas de convidados).

 c. Em **certificado de assinatura de SAML**, clique em **criar novo certificado**.

 d. Para começar a configurar o seu inquilino de Box.com para utilizar o Azure AD como um fornecedor de identidade, transfira o ficheiro de metadados e, em seguida, guarde-o à sua unidade local.

 e. Reencaminhar o ficheiro de metadados para a caixa de suportar equipa, que configura início de sessão único para si.

3. Para a configuração de utilizador automáticas do Azure AD, no painel esquerdo, selecione **aprovisionamento**e, em seguida, selecione **autorizar**.

  ![Autorizar o Azure AD para ligar à caixa](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Como os convidados precisam de Dropbox, os convidados precisam de caixa tem resgatar as respetivas convite a partir da aplicação caixa.

## <a name="next-steps"></a>Passos seguintes

Consulte os artigos seguintes na colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriedades de utilizador de colaboração B2B](active-directory-b2b-user-properties.md)
* [A adição de um utilizador de colaboração B2B a uma função](active-directory-b2b-add-guest-to-role.md)
* [Delegar convites para colaboração do B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinâmicos e de colaboração B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboração do B2B e exemplos do PowerShell](active-directory-b2b-code-samples.md)
* [Tokens de utilizador de colaboração B2B](active-directory-b2b-user-token.md)
* [Afirmações de utilizador de colaboração B2B mapeamento](active-directory-b2b-claims-mapping.md)
* [Partilha externa do Office 365](active-directory-b2b-o365-external-user.md)
* [Limitações atuais de colaboração B2B](active-directory-b2b-current-limitations.md)
