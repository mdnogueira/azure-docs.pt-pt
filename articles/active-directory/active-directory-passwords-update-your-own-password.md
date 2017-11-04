---
title: 'Azure AD: Repor a Palavra-passe | Microsoft Docs'
description: "Utilizar o self-service reposição palavra-passe para recuperar o acesso à sua conta de utilizador ou escola"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: gahug
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 8f175f5711b9798153d209f6645aa8bae9f2a00b
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="help-i-forgot-my-azure-ad-password"></a>Obter ajuda, esqueci minha palavra-passe do Azure AD

Se se tiver esquecido da sua palavra-passe, o seu departamento de TI nunca lhe enviou uma, a sua conta foi bloqueada ou se quiser alterá-la, podemos ajudá-lo. Se conhecer a palavra-passe e apenas necessário para alterar esta continuar para baixo para a secção [alterar a minha palavra-passe](#change-my-password) abaixo.

   > [!NOTE]
   > Se estiver a tentar voltar à sua conta pessoal, como a Xbox, hotmail.com ou outlook.com tente o [sugestões encontrada neste artigo](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Repor ou desbloquear a palavra-passe de uma conta escolar ou profissional

Se que não é possível aceder à sua conta devido a um dos seguintes:

* A palavra-passe não está a funcionar e gostaria de repô-lo.
* Conhecer a palavra-passe, mas a conta está bloqueada e pretende desbloquear a conta.

Siga os passos que se seguem para aceder à reposição de palavra-passe self-service do Azure AD, SSPR como podemos como chamá-lo e voltar à sua conta.

1. A partir de qualquer página de início de sessão escolar ou profissional, clique na ligação **Não consegue aceder à sua conta?** e, em seguida, clique em **Conta escolar ou profissional** ou aceda diretamente à [página de reposição de palavras-passe](https://passwordreset.microsoftonline.com/)

    ![Não consegue aceder à sua conta?][Login]

2. Introduza o seu **ID de Utilizador** escolar ou profissional, introduza os carateres que vê no ecrã para provar que não é um robô e clique em **Seguinte**.

   > [!NOTE]
   > Se o seu departamento de TI não tiver ativado esta funcionalidade, aparece uma ligação que diz “contacte o seu administrador”, para que o departamento o possa ajudar através de e-mail ou de um portal Web do próprio departamento.
   >
   > Se precisar de desbloquear a conta, neste momento escolha a opção "Posso minha palavra-passe, mas ainda não é possível iniciar sessão".
   >

3. Dependendo de como a sua equipa de TI tiver configurado SSPR vir um ou mais dos seguintes métodos de autenticação. Ou sua equipa de TI ter preenchido algumas destas informações antes de utilizar o artigo [registar para a reposição de palavra-passe self-service](active-directory-passwords-reset-register.md).

   * **Enviar e-mail para o meu e-mail alternativo**
   * **Enviar mensagem de texto para o meu telemóvel**
   * **Ligar para o meu telemóvel**
   * **Ligar para o meu telefone do escritório**
   * **Responder às minhas perguntas de segurança**

   Escolha uma opção, dê as respostas corretas e clique em **Seguinte**.

   ![Verifique os dados de autenticação][Verification]

4. O seu departamento de TI poderá precisar de mais confirmações, pelo que é possível que tenha de repetir o passo 3 com outra opção.
5. Na página **Escolher uma nova palavra-passe**, introduza uma palavra-passe nova, confirme-a e, em seguida, clique em **Concluir**. Sugerimos que a palavra-passe tenha entre 8 e 16 carateres com letras maiúsculas e minúsculas, números e carateres especiais.
6. Quando vir **A sua palavra-passe foi reposta**, pode iniciar sessão com a mesma.

    ![A palavra-passe do utilizador foi reposta][Complete]

Já deverá conseguir aceder à sua conta. Se não, contacte o departamento de TI da sua organização para obter mais ajuda.

Poderá receber um e-mail de confirmação de uma conta, como "Microsoft em nome de \<organização >". Se receber um e-mail assim e não utilizou a reposição de palavra-passe self-service para recuperar o acesso à sua conta, contacte o departamento de TI da sua organização.

## <a name="change-my-password"></a>Alterar a minha palavra-passe

Se já souber a palavra-passe e quiser alterá-la, utilize os passos que se seguem para o fazer.

### <a name="change-your-password-from-the-office-365-portal"></a>Alterar a palavra-passe a partir do portal do Office 365

Utilize este método se aceder às suas aplicações através do portal do Office normalmente

1. Inicie sessão no seu [conta do Office 365](https://www.office.com) utilizando a palavra-passe existente
2. Clique no seu perfil, no canto superior direito, e clique em **Ver conta**
3. Clique em **Segurança e Privacidade** > **Password**
4. Introduza a palavra-passe antiga, defina e confirme a nova e clique em **Submeter**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Alterar a palavra-passe a partir do Painel de Acesso do Azure

Utilize este método se aceder às suas aplicações através do Portal de Acesso do Azure normalmente

1. Inicie sessão no [Portal de Acesso do Azure](https://myapps.microsoft.com/) através da palavra-passe existente
2. Clique no seu perfil, no canto superior direito, e clique em **Perfil**
3. Clique em **Alterar palavra-passe**
4. Introduza a palavra-passe antiga, defina e confirme a nova e clique em **Submeter**.

## <a name="common-problems-and-their-solutions"></a>Problemas comuns e as respetivas soluções

 Eis alguns casos de erro comuns e as respetivas soluções:

| Caso de erro| O erro vir?| Solução |
| --- | --- | --- |
| Posso obter uma página ". Contacte o administrador" depois de introduzir o meu ID de utilizador | Contacte o administrador <br> <br> Detetámos que a palavra-passe da conta de utilizador não é gerida pela Microsoft. Como resultado, não conseguimos automaticamente repor a palavra-passe. <br> <br> Terá de contactar a sua equipa de TI para obter mais assistência. | Está a ver esta mensagem porque a sua equipa de TI gere a palavra-passe no seu ambiente no local e não permite-lhe repor a palavra-passe do não é possível aceder a ligação de conta. <br> <br> Para repor a palavra-passe, contacte a equipa de TI diretamente para obter ajuda e informar de que pretende repor a palavra-passe, pelo que podem ativar esta funcionalidade para si.|
| Posso obter um erro "a conta não está ativada para a reposição de palavra-passe" depois de introduzir o meu ID de utilizador | A sua conta não permite reposição da palavra-passe <br> <br> Lamentamos, mas a sua equipa de TI não tenha configurado a sua conta para utilização com este serviço. <br> <br> Se quiser, podemos contactá um administrador na sua organização para repor a palavra-passe por si. | Está a ver esta mensagem porque a sua equipa de TI não activou a reposição da sua organização de palavra-passe de não é possível aceder a ligação de conta ou ainda não está licenciado para utilizar a funcionalidade. <br> <br> Para repor a palavra-passe, clique o contacto uma ligação de administrador para enviar uma mensagem de e-mail da sua empresa e informar de que pretende repor a palavra-passe, pelo que podem ativar esta funcionalidade para si da equipa de TI. |
| Posso obter um erro de "pode não verificar a sua conta" depois de introduzir o meu ID de utilizador | Não foi possível verificar a sua conta <br> <br> Se quiser, podemos contactá um administrador na sua organização para repor a palavra-passe por si. | Está a ver esta mensagem porque estão ativados para a reposição de palavra-passe, mas não tiver registado para utilizar o serviço. Para registar para a reposição de palavra-passe, aceda ao http://aka.ms/ssprsetup após ter recuperou o acesso à sua conta. <br> <br> Para repor a palavra-passe, clique o contacto uma ligação de administrador para enviar uma mensagem de e-mail da sua empresa equipa de TI. |

## <a name="next-steps"></a>Passos seguintes

* [Como se registar para utilizar a reposição personalizada de palavra-passe](active-directory-passwords-reset-register.md)
* [Página de registo de reposição de palavra-passe](http://aka.ms/ssprsetup)
* [Portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/)
* [Não consegue iniciar sessão com a sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Página de início de sessão Não consegue aceder à sua conta?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Verifique os dados de autenticação"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Alterar a palavra-passe"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "A palavra-passe do utilizador foi reposta"
