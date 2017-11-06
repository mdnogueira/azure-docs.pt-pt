---
title: "Implementação: Azure AD SSPR | Microsoft Docs"
description: "Sugestões para a implementação bem-sucedida da reposição de palavras-passe self-service do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: e28324fe0d7e52f1721af6cd835369f024d4c58f
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="how-to-successfully-rollout-self-service-password-reset"></a>Como implementar com êxito a reposição personalizada de palavra-passe

A maioria dos clientes seguem estes passos para garantir a implementação eficaz da funcionalidade SSPR.

1. [Ative a reposição de palavras-passe no seu diretório](active-directory-passwords-getting-started.md).
2. [Configure as permissões do AD no local para repetição de escrita de palavras-passe](active-directory-passwords-writeback.md#active-directory-permissions).
3. [Configurar a repetição de escrita de palavras-passe](active-directory-passwords-writeback.md#configuring-password-writeback) para gravar palavras-passe do Azure AD no seu diretório local.
4. [Atribua e verifique as licenças necessárias](active-directory-passwords-licensing.md).
5. Se pretender implementar a SSPR gradualmente, pode limitar o acesso a um grupo de utilizadores, para que possa criar um piloto com um grupo específico. Para tal, mude o comutador **Reposição Personalizada de Palavra-Passe Ativada** para **Selecionado** e selecione o grupo de segurança no qual ativar a reposição de palavras-passe. 
6. Preencha os [Dados de Autenticação](active-directory-passwords-data.md) para os seus utilizadores, como o telefone do escritório, número de telemóvel e o endereço de e-mail alternativo.
7. [Personalize a experiência de início de sessão do Azure AD para incluir a imagem corporativa da sua empresa](active-directory-passwords-customize.md).
8. Ensinar os utilizadores a utilizar o SSPR, ao enviar-lhes instruções para que vejam como se registar e repor as palavras-passe.
9. Pode optar por forçar o registo em qualquer momento e exigir aos utilizadores que confirmem as informações de autenticação deles após um determinado período de tempo.
10. Ao longo do tempo, veja os [relatórios disponibilizados pelo Azure AD](active-directory-passwords-reporting.md) para rever os utilizadores que se registam e a utilizam.
11. Quando estiver pronto, ative a reposição de palavra-passe para todos os utilizadores, defina o comutador **Reposição Personalizada de Palavra-Passe Ativada** para **Todos**. 

    > [!IMPORTANT]
    > Teste o SSPR com um utilizador e não com um administrador, pois a Microsoft aplica requisitos de autenticação fortes às contas de tipo administrador do Azure. Para obter mais informações sobre a política de palavras-passe de administrador, veja o nosso [artigo técnico detalhado](active-directory-passwords-how-it-works.md).

## <a name="email-based-rollout"></a>Implementação baseado em e-mails

Para muitos clientes, a forma mais fácil de pôr os utilizadores a utilizar o SSPR é criar campanhas de e-mail, com instruções de utilização simples. [Criámos três e-mails simples que pode utilizar como modelos para ajudar na sua implementação.](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* O modelo de e-mail **Brevemente**, que pode ser utilizado nas semanas ou dias anteriores à implementação, para dizer aos utilizadores que precisam de fazer uma determinada coisa.
* O modelo de e-mail **Disponível Agora**, para ser utilizado no dia do lançamento, de modo a levar os utilizadores a registarem-se e confirmarem os respetivos dados de autenticação, para que possam utilizar o SSPR quando precisarem.
* O modelo de e-mail **Lembrete de Inscrição**, para ser utilizado uns dias ou semanas após a implementação, para relembrar aos utilizadores para se registarem e confirmarem os respetivos dados de autenticação.

![E-mail][Email]

## <a name="creating-your-own-password-portal"></a>Criar o seu próprio portal de palavras-passe

Muitos dos nossos clientes maiores optam por alojar a página Web e criar uma entrada DNS de raiz, como https://passwords.contoso.com. Preenchem esta página com ligações para a reposição de palavras-passe do Azure AD, o registo de reposição de palavras-passe, os portais de alteração de palavras-passe e outras informações específicas da organização. Depois, pode incluir, em qualquer comunicação ou panfleto por e-mail que distribuir, um URL corporativo e memorável, ao qual os utilizadores podem aceder quando precisam de utilizar os serviços.

* Portal para reposição de palavras-passe - https://aka.ms/sspr
* Portal de registo da reposição de palavras-passe - http://aka.ms/ssprsetup
* Portal de alteração de palavras-passe - https://account.activedirectory.windowsazure.com/ChangePassword.aspx

Para seu benefício, criámos uma página de exemplo, que pode utilizar e personalizar consoante as necessidades da sua organização, que pode ser transferida a partir do [GitHub](https://github.com/ajamess/password-reset-page).

## <a name="using-enforced-registration"></a>Utilizar o registo forçado

Se quiser que os utilizadores se registem na reposição de palavras-passe, pode forçá-los a registarem-se quando iniciam sessão com o Azure AD. Pode ativar esta opção no painel **Reposição de palavras-passe** do seu diretório, ao ativar a opção **Exigir que os Utilizadores se Registem Quando Iniciam Sessão**, no separador **Registo**.

Os administradores podem exigir aos utilizadores que se registem após um período de tempo, ao definir o **Número de dias antes de ser pedido aos utilizadores que reconfirmem as respetivas informações de autenticação** entre 0 e 730 dias.

Depois de ativar esta opção, os utilizadores que iniciem sessão verão uma mensagem que os informa de que o administrador lhes exigiu que verifiquem as informações de autenticação deles.

## <a name="populate-authentication-data"></a>Preencher os dados de autenticação

Se [preencher os dados de autenticação pelos seus utilizadores](active-directory-passwords-data.md), estes não têm de se registar na reposição de palavras-passe antes de poderem utilizar o SSPR. Desde que os utilizadores tenham os dados de autenticação definidos de forma a que cumpram a política de reposição de palavras-passe, podem repor as palavras-passe deles.

## <a name="disabling-self-service-password-reset"></a>Desativar a reposição de palavras-passe self-service

Para desativar a reposição de palavras-passe self-service basta abrir o inquilino do Azure AD e aceder a **Reposição de Palavras-passe**, **Propriedades** e escolher **Ninguém**, em **Reposição de Palavras-passe Self-service Ativada**

## <a name="next-steps"></a>Passos seguintes

* [Reponha ou altere a palavra-passe](active-directory-passwords-update-your-own-password.md).
* [Registe-se na reposição personalizada de palavras-passe](active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre Licenciamento?](active-directory-passwords-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](active-directory-passwords-data.md)
* [Quais são as opções de política da SSPR?](active-directory-passwords-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](active-directory-passwords-writeback.md)
* [Como posso comunicar a atividade da SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](active-directory-passwords-how-it-works.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Email]: ./media/active-directory-passwords-best-practices/sspr-emailtemplates.png "Personalizar estes modelos de e-mail para satisfazer as suas necessidades organizacionais"