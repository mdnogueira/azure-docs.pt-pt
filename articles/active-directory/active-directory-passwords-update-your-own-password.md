---
title: 'Azure AD: Repor a Palavra-passe | Microsoft Docs'
description: "Utilize a reposição personalizada de palavra-passe para recuperar o acesso à sua conta"
services: active-directory
keywords: "Gestão de palavra-passe do Active Directory, gestão de palavra-passe, reposição personalizada de palavra-passe do Azure AD, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c95f52f028cdfaed35821275d816a24035ff02fb
ms.lasthandoff: 04/25/2017


---
# <a name="help-i-forgot-my-azure-ad-password"></a>Ajudem-me, esqueci-me da minha palavra-passe do Azure AD

Se se tiver esquecido da sua palavra-passe, o seu departamento de TI nunca lhe enviou uma, a sua conta foi bloqueada ou se quiser alterá-la, podemos ajudá-lo.

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Repor ou desbloquear a palavra-passe de uma conta escolar ou profissional

Para aceder à sua conta escolar ou profissional, siga os passos abaixo para aceder à reposição de palavras-passe self-service do Azure AD, ou SSPR, como gostamos de dizer.

1. A partir de qualquer página de início de sessão escolar ou profissional, clique na ligação **Não consegue aceder à sua conta?** e, em seguida, clique em **Conta escolar ou profissional** ou aceda diretamente à [página de reposição de palavras-passe](https://passwordreset.microsoftonline.com/)

   > [!NOTE]
   > Se estiver a tentar voltar a uma Conta pessoal, como hotmail.com ou outlook.com, experimente as [sugestões disponíveis neste artigo](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
   >
    ![Não consegue aceder à sua conta?][Login]

2. Introduza o seu **ID de Utilizador** escolar ou profissional, introduza os carateres que vê no ecrã para provar que não é um robô e clique em **Seguinte**.

   > [!NOTE]
   > Se o seu departamento de TI não tiver ativado esta funcionalidade, aparece uma ligação que diz “contacte o seu administrador”, para que o departamento o possa ajudar através de e-mail ou de um portal Web do próprio departamento.
   >

3. Dependendo da forma como o departamento de TI configurou a SSPR, verá uma ou mais das opções seguintes. Algumas [destas](active-directory-passwords-reset-register.md) informações foram preenchidas por si ou pelo seu departamento de TI antes de terem sido utilizadas.
    * **Enviar e-mail para o meu e-mail alternativo**
    * **Enviar mensagem de texto para o meu telemóvel**
    * **Ligar para o meu telemóvel**
    * **Ligar para o meu telefone do escritório**
    * **Responder às minhas perguntas de segurança**

    Escolha uma opção, dê as respostas corretas e clique em **Seguinte**.

    ![Verifique os dados de autenticação][Verification]

4. O seu departamento de TI poderá precisar de mais confirmações, pelo que é possível que tenha de repetir o passo 3 com outra opção.
5. Na página **Escolher uma nova palavra-passe**, introduza uma palavra-passe nova, confirme-a e, em seguida, clique em **Concluir**. Sugerimos que a palavra-passe tenha entre 8 e 16 carateres com letras maiúsculas e minúsculas, números e carateres especiais.

   > [!NOTE]
   > Se precisava de desbloquear a sua conta, nesta fase, escolha a opção para desbloquear apenas ou altere a palavra-passe e desbloqueie.
   >

6. Quando vir **A sua palavra-passe foi reposta**, pode iniciar sessão com a mesma.

    ![A palavra-passe do utilizador foi reposta][Complete]

Já deverá conseguir aceder à sua conta. Se não, contacte o departamento de TI da sua organização para obter mais ajuda.

Poderá receber um e-mail de confirmação proveniente de uma conta como "Microsoft, em nome da \<sua organização >\". Se receber um e-mail assim e não utilizou a reposição de palavra-passe self-service para recuperar o acesso à sua conta, contacte o departamento de TI da sua organização.

## <a name="change-my-password"></a>Alterar a minha palavra-passe

Se já souber a palavra-passe e quiser alterá-la, utilize os passos que se seguem para o fazer.

### <a name="change-your-password-from-the-office-365-portal"></a>Alterar a palavra-passe a partir do portal do Office 365

Utilize este método se aceder às suas aplicações através do portal do Office normalmente

1. Iniciar sessão na sua [conta do Office 365](https://www.office.com)
2. Clique no seu perfil, no canto superior direito, e clique em **Ver conta**
3. Clique em **Segurança e Privacidade** > **Password**
4. Introduza a palavra-passe antiga, defina e confirme a nova e clique em **Submeter**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Alterar a palavra-passe a partir do Painel de Acesso do Azure

Utilize este método se aceder às suas aplicações através do Portal de Acesso do Azure normalmente

1. Inicie sessão no [Portal de Acesso do Azure](https://myapps.microsoft.com/) através da palavra-passe existente
2. Clique no seu perfil, no canto superior direito, e clique em **Perfil**
3. Clique em **Alterar palavra-passe**
4. Introduza a palavra-passe antiga, defina e confirme a nova e clique em **Submeter**.

## <a name="next-steps"></a>Passos Seguintes

* [Como se registar para utilizar a reposição personalizada de palavra-passe](active-directory-passwords-reset-register.md)
* [Página de registo de reposição de palavra-passe](http://aka.ms/ssprsetup)
* [Portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/)
* [Não consegue iniciar sessão com a sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Página de início de sessão Não consegue aceder à sua conta?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Verifique os dados de autenticação"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Alterar a palavra-passe"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "A palavra-passe do utilizador foi reposta"

