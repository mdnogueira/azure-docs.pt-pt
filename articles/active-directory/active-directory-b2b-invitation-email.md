---
title: "Os elementos do e-mail de convite de colaboração do Azure Active Directory B2B | Microsoft Docs"
description: "Modelo do e-mail de convite de colaboração do Azure Active Directory B2B"
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
ms.openlocfilehash: 458a2cab13b7e83f120e0926a95d454070181dfb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email"></a>Os elementos do e-mail de convite de colaboração B2B

Mensagens de e-mail de convite são um componente crítico de traga parceiros no board como utilizadores de colaboração B2B do Azure AD. Pode utilizá-los para aumentar a confiança do destinatário. Pode adicionar legitimidade e prova redes social para o e-mail, para se certificar de que o destinatário de um aspeto semelhante confortável com a seleção de **começar** botão para aceitar o convite. Esta confiança é que uma chave significa reduzir friction partilha. E também deve certificar-o e-mail parecer excelente!

![E-mail de convite do Azure AD B2b](media/active-directory-b2b-invitation-email/invitation-email.png)

## <a name="explaining-the-email"></a>Explicar o e-mail
Vamos ver alguns elementos da mensagem de e-mail para saber como melhor para utilizar as respetivas capacidades.

### <a name="subject"></a>Assunto
O assunto do e-mail segue o padrão seguinte: está convidado para o &lt;tenantname&gt; organização

### <a name="from-address"></a>Endereço de
Podemos utilizar um padrão como o LinkedIn para o endereço.  Deve ser claro que seja o inviter e endereço de e-mail da empresa e também esclarecer que o e-mail é proveniente de um Microsoft. O formato é: &lt;nome a apresentar do inviter&gt; de &lt;tenantname&gt; (através do Microsoft) <invites@microsoft.com&gt;

### <a name="reply-to"></a>Responda a
O e-mail de resposta é definido ao e-mail a inviter se estiver disponível, para que a resposta para o e-mail envia um e-mail para o inviter.

### <a name="branding"></a>Imagem corporativa
Os e-mails convite da sua utilização de inquilino a imagem corporativa que pode ter configurado para o seu inquilino. Se pretender tirar partido desta capacidade [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) se os detalhes sobre como configurá-lo. O logótipo de faixa aparece na mensagem de correio eletrónico. Siga o tamanho da imagem e instruções de qualidade [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) para obter os melhores resultados. Além disso, o nome da empresa também aparece na chamada para a ação.

### <a name="call-to-action"></a>Chamada para a acção
A chamada de ação é composto por duas partes: explicar por que motivo o destinatário recebeu correio e o que está a ser pedido o destinatário fazer acerca do mesmo.
- A secção "Porquê" pode ser resolvida utilizando o padrão do seguinte: já foi convidado para aceder às aplicações no &lt;tenantname&gt; organização

- E o "o seu está a ser pedido para" secção indicada pela presença do **começar** botão. Quando o destinatário tenha sido adicionado sem a necessidade de convites, este botão não aparecer.

### <a name="inviters-information"></a>Informações do inviter
Nome a apresentar o inviter está incluído no e-mail. E, além disso, se tiver configurado a uma imagem de perfil para a sua conta do Azure AD, o e-mail convidando irá incluir essa imagem bem. Ambos destinam-se para aumentar a confiança do destinatário do e-mail.

Se ainda não configurou a imagem do perfil, é apresentado um ícone com iniciais do inviter em vez da imagem:

  ![Apresentar iniciais do inviter](media/active-directory-b2b-invitation-email/inviters-initials.png)

### <a name="body"></a>Corpo
O corpo contém a mensagem de que o inviter composes ou é transferido o convite API. É uma área de texto, pelo que não processa tags de HTML por motivos de segurança.

### <a name="footer-section"></a>Secção de rodapé
O rodapé contém a marca de empresa da Microsoft e permite que o destinatário saber se o e-mail foi enviado a partir de um alias de não monitorizado. Casos especiais:

- O inviter não tiver um endereço de e-mail nos inquilinos convidando

  ![imagem da inviter não tiver um endereço de e-mail nos inquilinos convidando](media/active-directory-b2b-invitation-email/inviter-no-email.png)


- O destinatário não necessário resgatar o convite

  ![Quando não precisa de destinatário resgatar convite](media/active-directory-b2b-invitation-email/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>Passos seguintes

Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como é que os administradores de Azure Active Directory adicionar utilizadores de colaboração do B2B?](active-directory-b2b-admin-add-users.md)
* [Como é que os infotrabalhadores adicionar utilizadores de colaboração do B2B?](active-directory-b2b-iw-add-users.md)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento e colaboração do Azure AD B2B](active-directory-b2b-licensing.md)
* [Resolução de problemas de colaboração B2B do Azure Active Directory do](active-directory-b2b-troubleshooting.md)
* [Colaboração do Azure Active Directory B2B perguntas mais frequentes (FAQ)](active-directory-b2b-faq.md)
* [Colaboração B2B do Active Directory Azure API e personalização](active-directory-b2b-api.md)
* [Autenticação multifator para os utilizadores da colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Adicionar utilizadores de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
