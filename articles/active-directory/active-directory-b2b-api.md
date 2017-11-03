---
title: "Colaboração B2B do Active Directory Azure API e personalização | Microsoft Docs"
description: "A colaboração do B2B Azure Active Directory suporta as relações entre empresas, permitindo a parceiros de negócios acederem, seletivamente, às suas aplicações empresariais"
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
ms.date: 04/11/2017
ms.author: sasubram
ms.openlocfilehash: c85e05b38b4a9525e13ec510a17b7ef4841198d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Colaboração B2B do Active Directory Azure API e personalização

Iremos tenha tido muitos clientes diga-nos de que pretende personalizar o processo de convite de uma forma que funciona melhor das suas organizações. Com a nossa API, pode fazê-lo imediatamente. [https://Developer.microsoft.com/Graph/Docs/API-Reference/v1.0/Resources/Invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Capacidades do convite API
A API oferece as seguintes capacidades:

1. Convidar um utilizador externo com *qualquer* endereço de e-mail.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Personalize onde pretende que os utilizadores encaminhado para assim que aceitarem os respetivos convite.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Optar por enviar o correio de convite padrão através dos EUA

    ```
    "sendInvitationMessage": true
    ```

  com uma mensagem para o destinatário pode personalizar

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. E optar por cc: pessoas que pretende manter no ciclo sobre o inviting este colaborador.

5. Ou personalizar por completo o convite e fluxo de trabalho de integração ao optar por não enviar notificações através do Azure AD.

    ```
    "sendInvitationMessage": false
    ```

  Neste caso, regressar um URL de resgate partir da API de poder incorporar um modelo de correio eletrónico, MI ou outro método de distribuição da sua preferência.

6. Por fim, se for um administrador, pode optar por convidar o utilizador como membro.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Modelo de autorização
A API pode ser executada num seguintes modos de autorização:

### <a name="app--user-mode"></a>Aplicação + modo de utilizador
Neste modo, quem está a utilizar as necessidades de API para ter as permissões para ser criar convites B2B.

### <a name="app-only-mode"></a>Modo só de aplicação
No contexto de aplicação apenas a aplicação tem dos âmbitos User.ReadWrite.All ou Directory.ReadWrite.All para o convite com êxito.

Para obter mais informações, consulte: https://graph.microsoft.io/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell
Agora é possível utilizar o PowerShell para adicionar e convidar utilizadores externos para uma organização facilmente. Crie um convite utilizando o cmdlet:

```
New-AzureADMSInvitation
```

Pode utilizar as seguintes opções:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

Também pode consultar a referência de convite de API no [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="next-steps"></a>Passos seguintes

Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como é que os administradores de Azure Active Directory adicionar utilizadores de colaboração do B2B?](active-directory-b2b-admin-add-users.md)
* [Como é que os infotrabalhadores adicionar utilizadores de colaboração do B2B?](active-directory-b2b-iw-add-users.md)
* [Os elementos do e-mail de convite de colaboração B2B](active-directory-b2b-invitation-email.md)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento e colaboração do Azure AD B2B](active-directory-b2b-licensing.md)
* [Resolução de problemas de colaboração B2B do Azure Active Directory do](active-directory-b2b-troubleshooting.md)
* [Colaboração do Azure Active Directory B2B perguntas mais frequentes (FAQ)](active-directory-b2b-faq.md)
* [Autenticação multifator para os utilizadores da colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Adicionar utilizadores de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
