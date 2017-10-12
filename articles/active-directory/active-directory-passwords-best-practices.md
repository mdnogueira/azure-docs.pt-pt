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
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 4cfc1652377f0cfd059e336aec6994b40d32c559
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="roll-out-password-reset-for-users"></a>Implementar a reposição de palavras-passe para os utilizadores

A maioria dos clientes seguem os passos abaixo para garantir a implementação eficaz da funcionalidade SSPR.

1. [Ativar a reposição de palavras-passe no seu diretório](active-directory-passwords-getting-started.md)
2. [Configurar as permissões do AD no local para repetição de escrita de palavras-passe](active-directory-passwords-how-it-works.md#active-directory-permissions)
3. [Configurar a repetição de escrita de palavras-passe](active-directory-passwords-writeback.md#configuring-password-writeback) para gravar palavras-passe do Azure AD no seu diretório local
4. [Atribuir e verificar as licenças necessárias](active-directory-passwords-licensing.md)
5. Se quiser implementar de forma gradual, pode, opcionalmente, limitar a reposição de palavras-passe a um grupo de utilizadores, para disponibilizar a funcionalidade devagar e ao longo do tempo. Para tal, mude o interruptor **Reposição de Palavras-passe Self-service Ativada** de **Toda a gente** para **Um grupo** e selecione um grupo de segurança no qual ativar a reposição de palavras-passe. Os membros deste grupo têm de lhes ter todas as licenças atribuídas e é uma excelente forma de ativar o [Licenciamento Baseado em Grupo](active-directory-passwords-licensing.md#enable-group-or-user-based-licensing).
6. Preencher o conjunto mínimo de [Dados de Autenticação](active-directory-passwords-data.md), com base na sua política.
7. Ensinar os utilizadores a utilizar o SSPR, ao enviar-lhes instruções para que vejam como se registar e repor as palavras-passe.
    > [!NOTE]
    > Teste o SSPR com um utilizador e não com um administrador, pois a Microsoft aplica requisitos de autenticação fortes às contas de tipo administrador do Azure. Para obter mais informações sobre a política de palavras-passe de administrador, veja o nosso [artigo técnico detalhado](active-directory-passwords-how-it-works.md).

8. Pode optar por forçar o registo em qualquer momento e exigir aos utilizadores que confirmem as informações de autenticação deles após um determinado período de tempo. Se não quiser que os utilizadores se registem, pode [implementar a reposição de palavras-passe sem exigir o registo do utilizador final](active-directory-passwords-data.md).
9. Ao longo do tempo, veja os [relatórios disponibilizados pelo Azure AD](active-directory-passwords-reporting.md) para rever os utilizadores que se registam e a utilizam.

## <a name="email-based-rollout"></a>Implementação baseado em e-mails

Para muitos clientes, a forma mais fácil de pôr os utilizadores a utilizar o SSPR é criar campanhas de e-mail, com instruções de utilização simples. [Criámos três e-mails simples que pode utilizar como modelos para ajudar na sua implementação.](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* O modelo de e-mail **Brevemente**, que pode ser utilizado nas semanas ou dias anteriores à implementação, para dizer aos utilizadores que precisam de fazer uma determinada coisa.
* O modelo de e-mail **Disponível Agora**, para ser utilizado no dia do lançamento, de modo a levar os utilizadores a registarem-se e confirmarem os respetivos dados de autenticação, para que possam utilizar o SSPR quando precisarem.
* O modelo de e-mail **Lembrete de Inscrição**, para ser utilizado uns dias ou semanas após a implementação, para relembrar aos utilizadores para se registarem e confirmarem os respetivos dados de autenticação.

## <a name="creating-your-own-password-portal"></a>Criar o seu próprio portal de palavras-passe

Muitos dos nossos clientes maiores optam por alojar a página Web e criar uma entrada DNS de raiz, como https://passwords.contoso.com. Preenchem esta página com ligações para a reposição de palavras-passe do Azure AD, o registo de reposição de palavras-passe, os portais de alteração de palavras-passe e outras informações específicas da organização. Depois, pode incluir, em qualquer comunicação ou panfleto por e-mail que distribuir, um URL corporativo e memorável, ao qual os utilizadores podem aceder quando precisam de utilizar os serviços.

* Portal de reposição de palavras-passe - https://passwordreset.microsoftonline.com/
* Portal de registo da reposição de palavras-passe - http://aka.ms/ssprsetup
* Portal de alteração de palavras-passe - https://account.activedirectory.windowsazure.com/ChangePassword.aspx

## <a name="using-enforced-registration"></a>Utilizar o registo forçado

Se quiser que os utilizadores se registem na reposição de palavras-passe, pode forçá-los a registarem-se quando iniciam sessão com o Azure AD. Pode ativar esta opção no painel **Reposição de palavras-passe** do seu diretório, ao ativar a opção **Exigir que os Utilizadores se Registem Quando Iniciam Sessão**, no separador **Registo**.

Os administradores podem exigir aos utilizadores que se registem após um período de tempo, ao definir o **Número de dias antes de ser pedido aos utilizadores que reconfirmem as respetivas informações de autenticação** entre 0 e 730 dias.

Depois de ativar esta opção, os utilizadores que iniciem sessão verão uma mensagem que os informa de que o administrador lhes exigiu que verifiquem as informações de autenticação deles.

## <a name="populate-authentication-data"></a>Preencher os dados de autenticação

Se [preencher os dados de autenticação pelos seus utilizadores](active-directory-passwords-data.md), estes não têm de se registar na reposição de palavras-passe antes de poderem utilizar o SSPR. Desde que os utilizadores tenham os dados de autenticação definidos de forma a que cumpram a política de reposição de palavras-passe, podem repor as palavras-passe deles.

## <a name="disabling-self-service-password-reset"></a>Desativar a reposição de palavras-passe self-service

Para desativar a reposição de palavras-passe self-service basta abrir o inquilino do Azure AD e aceder a **Reposição de Palavras-passe**, **Propriedades** e escolher **Ninguém**, em **Reposição de Palavras-passe Self-service Ativada**

## <a name="next-steps"></a>Passos seguintes

As ligações seguintes disponibilizam informações adicionais relativamente à reposição de palavras-passe com o Azure AD

* [**Guia de Introdução** ](active-directory-passwords-getting-started.md) - Começar a trabalhar com a gestão de palavras-passe self-service do Azure AD 
* [**Licensing**](active-directory-passwords-licensing.md) - Configure your Azure AD Licensing (Licenciamento - Configurar o Licenciamento do Azure AD)
* [**Data**](active-directory-passwords-data.md) - Understand the data that is required and how it is used for password management (Dados - Compreender os dados necessários e de que forma são utilizados na gestão de palavras-passe)
* [**Customize**](active-directory-passwords-customize.md) - Customize the look and feel of the SSPR experience for your company (Personalizar - Personalizar o aspeto e a funcionalidade da experiência de SSPR da sua empresa)
* [**Policy**](active-directory-passwords-policy.md) - Understand and set Azure AD password policies (Política - Compreender e definir políticas de palavras-passe do Azure AD)
* [**Password Writeback**](active-directory-passwords-writeback.md) - How does password writeback work with your on-premises directory (Repetição de Escrita de Palavras-passe - Como funciona a repetição de escrita de palavras-passe com o seu diretório no local)
* [**Reporting**](active-directory-passwords-reporting.md) - Discover if, when, and where your users are accessing SSPR functionality (Relatórios - Descubra se, quando e a partir de onde é que os utilizadores acedem à funcionalidade SSPR)
* [**Technical Deep Dive**](active-directory-passwords-how-it-works.md) - Go behind the curtain to understand how it works (Análise Técnica - veja por detrás dos bastidores para saber como funciona)
* [**Frequently Asked Questions**](active-directory-passwords-faq.md) - How? Porquê? What? Where? Who? When? - Answers to questions you always wanted to ask (Perguntas Mais Frequentes - Como? Porquê? O quê? Onde? Quem? - Respostas para as perguntas que sempre quis saber)
* [**Troubleshoot**](active-directory-passwords-troubleshoot.md) - Learn how to resolve common issues that we see with SSPR (Resolução de Problemas - Saiba como resolver problemas comuns do SSPR)