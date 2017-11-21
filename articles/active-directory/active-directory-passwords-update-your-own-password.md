---
title: 'Azure AD: Repor a palavra-passe | Microsoft Docs'
description: "Utilizar o self-service reposição palavra-passe ao recuperar o acesso ao seu trabalho ou escola conta de utilizador"
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
ms.date: 11/13/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 6230256d16af0ef2b924d345ae86d5d44ecb6738
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="reset-your-work-or-school-password"></a>Repor a palavra-passe de conta escolar ou profissional

Se esqueceu a palavra-passe, nunca recebeu um do seu suporte de empresa, conta ter sido bloqueado ou pretende alterá-lo, podemos ajudar. Se conhecer a palavra-passe e apenas terá de alterá-la, avance para o [alterar a minha palavra-passe](#change-my-password) secção.

   > [!NOTE]
   > Se estiver a tentar aceder novamente à sua conta pessoal, como a Xbox, hotmail.com ou outlook.com, experimente as sugestões no [quando não é possível iniciar sessão na sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Repor ou desbloquear a palavra-passe de uma conta escolar ou profissional

Poderá não conseguir aceder à sua conta do Azure Active Directory (Azure AD) devido a um dos seguintes motivos:

* A palavra-passe não está a funcionar e pretender repô-lo.
* Conhecer a palavra-passe, mas a conta está bloqueada e quiser desbloqueá-la.

Utilize os seguintes passos para aceder à reposição de palavra-passe self-service do Azure AD (SSPR) e a aceder novamente à sua conta.

1. De qualquer trabalho ou escola **início de sessão** página, selecione o **não é possível aceder à sua conta?** associar e, em seguida, selecione **trabalhar conta escolar ou profissional** ou vá diretamente para o [ Página de reposição de palavra-passe](https://passwordreset.microsoftonline.com/).

    ![Não consegue aceder à sua conta?][Login]

2. Introduza a sua empresa ou escola **ID de utilizador**, não são um robot, introduzindo os carateres Consulte no ecrã e, em seguida, selecione de provar **seguinte**.

   > [!NOTE]
   > Se a sua equipa de TI não tiver ativado esta funcionalidade, é apresentada uma ligação "Contacte o administrador" para que possa ajudar a equipa de TI por e-mail ou um portal web os seus próprios.
   >
   > Se precisar de desbloquear a conta, neste momento selecione a opção **minha palavra-passe, mas ainda não é possível iniciar sessão.**
   >

3. Consoante a forma como a sua equipa de TI tiver configurado SSPR, deverá ver um ou mais dos seguintes métodos de autenticação. Ou sua equipa de TI deve ter preenchido algumas destas informações ao seguir os passos a [registar para a reposição de palavra-passe self-service](active-directory-passwords-reset-register.md) artigo.

   * **Enviar e-mail para o meu e-mail alternativo**
   * **Enviar mensagem de texto para o meu telemóvel**
   * **Ligar para o meu telemóvel**
   * **Ligar para o meu telefone do escritório**
   * **Responder às minhas perguntas de segurança**

   Escolher uma opção, forneça as respostas corretas e, em seguida, selecione **seguinte**.

   ![Verifique os dados de autenticação][Verification]

4. A equipa de TI pode necessitar de mais de verificação e poderá ter de repetir o passo 3 com uma opção diferente.
5. No **escolher uma nova palavra-passe** página, introduza uma nova palavra-passe, confirme a palavra-passe e, em seguida, selecione **concluir**. A palavra-passe de conta escolar ou profissional pode ter determinados requisitos que tem de cumprir. Sugerimos que escolha uma palavra-passe que 8 a 16 carateres de comprimento e inclui carateres em maiúsculas e minúsculas, um número e um caráter especial.
6. Quando vir a mensagem **a palavra-passe foi reposta**, pode iniciar sessão com a nova palavra-passe.

    ![A palavra-passe do utilizador foi reposta][Complete]

Agora deverá conseguir aceder à sua conta. Se não é possível aceder à sua conta, deverá contactar da sua organização a equipa de TI para obter ajuda adicional.

Poderá receber um e-mail de confirmação de uma conta, como "Microsoft em nome de \<organização >." Se obtiver uma mensagem de e-mail como este e não utilizou o self-service reposição palavra-passe para recuperar o acesso à sua conta, contacte da sua organização equipa de TI.

## <a name="change-my-password"></a>Alterar a minha palavra-passe

Se já a conhecer a palavra-passe e pretender alterá-lo, utilize os seguintes passos.

### <a name="change-your-password-from-the-office-365-portal"></a>Alterar a palavra-passe a partir do portal do Office 365

Utilize este método se aceder normalmente, as aplicações através do portal do Office:

1. Inicie sessão no seu [conta do Office 365](https://www.office.com) com a palavra-passe existente.
2. Selecione o perfil no lado superior direito e, em seguida, selecione **ver conta**.
3. Selecione **privacidade e segurança** > **palavra-passe**.
4. Introduza a palavra-passe antiga, definir e confirmar a sua nova palavra-passe e, em seguida, selecione **submeter**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Alterar a palavra-passe a partir do Painel de Acesso do Azure

Utilize este método se aceder normalmente as suas aplicações a partir do painel de acesso do Azure (MyApps):

1. Iniciar sessão para o [painel de acesso do Azure](https://myapps.microsoft.com/) com a palavra-passe existente.
2. Selecione o perfil no lado superior direito e, em seguida, selecione **perfil**.
3. Selecione **alterar palavra-passe**.
4. Introduza a palavra-passe antiga, definir e confirmar a sua nova palavra-passe e, em seguida, selecione **submeter**.

## <a name="reset-password-at-sign-in"></a>Repor palavra-passe no início de sessão

Se o administrador tem ativada a funcionalidade, agora, pode ver uma ligação para **Repor palavra-passe** no seu ecrã de início de sessão no Windows 10, enquadram-se criadores Update.

![Ecrã de início de sessão][LoginScreen]

Selecione o **Repor palavra-passe** ligação para abrir a experiência SSPR no ecrã de início de sessão para que pode repor a palavra-passe sem ter de iniciar sessão aceder a experiência baseada na web normal.

1. Confirme o seu ID de utilizador e selecione **seguinte**.
2. Selecione e confirmar um método de contacto para verificação. A equipa de TI pode necessitar de mais de verificação e poderá ter de repetir este passo com uma opção diferente.

   ![Método de contacto][ContactMethod]

3. No **criar uma nova palavra-passe** página, introduza uma nova palavra-passe, confirme a palavra-passe e, em seguida, selecione **seguinte**. Sugerimos que a palavra-passe é 8-16 carateres de comprimento e é composto de letras maiúsculas e minúsculas, números e carateres especiais.

   ![Repor palavra-passe][ResetPassword]

4. Quando vir a mensagem **a palavra-passe foi reposta**, selecione **concluir**.

Agora deverá conseguir aceder à sua conta. Se não, contacte da sua organização a equipa de TI para obter ajuda adicional.

## <a name="common-problems-and-their-solutions"></a>Problemas comuns e as respetivas soluções

 Eis alguns casos de erro comuns e as respetivas soluções:

| Caso de erro| O erro vir?| Solução |
| --- | --- | --- |
| Posso obter uma página ". Contacte o administrador" depois de introduzir o meu ID de utilizador | Contacte o administrador. <br> <br> Detetámos que a palavra-passe da conta de utilizador não é gerida pela Microsoft. Como resultado, não conseguimos automaticamente repor a palavra-passe. <br> <br> Terá de contactar a sua equipa de TI para obter assistência. | Está a ver esta mensagem porque a sua equipa de TI gere a palavra-passe no seu ambiente no local. Não é possível repor a palavra-passe de ligação "Não é possível aceder à sua conta". <br> <br> Para repor a palavra-passe, contacte a equipa de TI diretamente para obter ajuda e informar de que pretende repor a palavra-passe, pelo que podem ativar esta funcionalidade para si.|
| Posso obter um erro "a conta não está ativada para a reposição de palavra-passe" depois de introduzir o meu ID de utilizador | A conta não está ativada para a reposição de palavra-passe. <br> <br> Lamentamos, mas a sua equipa de TI não tenha configurado a sua conta para utilizar este serviço. <br> <br> Se quiser, podemos contactá um administrador na sua organização para repor a palavra-passe por si. | Está a ver esta mensagem porque a sua equipa de TI não activou a reposição da sua organização a partir da ligação "Não é possível aceder à sua conta" palavra-passe, ou ainda não está licenciado para utilizar a funcionalidade. <br> <br> Para repor a palavra-passe, selecione "Contacte uma ligação de administrador" para enviar uma mensagem de e-mail da sua empresa e informar de que pretende repor a palavra-passe, pelo que podem ativar esta funcionalidade para si da equipa de TI. |
| Posso obter um erro de "Pode não verificar a sua conta" depois de introduzir o meu ID de utilizador | Não foi possível verificar a sua conta. <br> <br> Se quiser, podemos contactá um administrador na sua organização para repor a palavra-passe por si. | Está a ver esta mensagem porque está a ser ativada para a reposição de palavra-passe, mas não tiver registado para utilizar o serviço. Para registar para a reposição de palavra-passe, aceda ao http://aka.ms/ssprsetup após ter recuperou o acesso à sua conta. <br> <br> Para repor a palavra-passe, selecione a hiperligação "Contacte um administrador" para enviar uma mensagem de e-mail da sua empresa equipa de TI. |

## <a name="next-steps"></a>Passos seguintes

* [Como se registar para utilizar a reposição personalizada de palavra-passe](active-directory-passwords-reset-register.md)
* [Página de registo de reposição de palavra-passe](http://aka.ms/ssprsetup)
* [Portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/)
* [Não consegue iniciar sessão com a sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Página de início de sessão Não consegue aceder à sua conta?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Verifique os dados de autenticação"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Alterar a palavra-passe"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "A palavra-passe do utilizador foi reposta"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Ligação do Windows 10, enquadram-se criadores Update início de sessão ecrã reposição de palavra-passe"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "Verifique os dados de autenticação"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Alterar a palavra-passe"

