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
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 9b7735bbe8d3b2ffc78eba8f1489d70a02fe7342
ms.lasthandoff: 04/12/2017


---
# <a name="help-i-forgot-my-password"></a>Ajuda, esqueci-me da minha palavra-passe

Passo 1... não entre em pânico

Se os cenários seguintes se aplicam a si, podemos ajudar

* Se não souber como aceder à sua conta e não se lembra da palavra-passe
* Não foi atribuída uma palavra-passe e o administrador enviou-o para aqui

## <a name="unlock-your-account"></a>Desbloquear a sua conta

Se o utilizador está aqui para desbloquear a conta, siga os passos abaixo. Quando vir **Escolher uma nova palavra-passe** no passo 6 abaixo, pode desbloquear ou alterar a palavra-passe.

## <a name="reset-my-password"></a>Repor a minha palavra-passe

Para voltar para a sua conta, siga estes passos abaixo.
1. A partir de qualquer página de início de sessão escolar ou profissional, clique na **ligação Não consegue aceder à sua conta?** e, em seguida, na **Conta profissional ou escolar** ou aceda diretamente à [página de reposição de palavra-passe](https://passwordreset.microsoftonline.com/)

    ![Não consegue aceder à sua conta?][Login]

2. Introduza o seu **ID de Utilizador** escolar ou profissional e prove que não é um robot ao passar no teste CAPTCHA e introduzir o texto apresentado, e clique em **Seguinte**
3. Dependendo de como o administrador configurou as coisas verá um ou mais dos seguintes procedimentos
    * **Enviar um e-mail para o meu e-mail alternativo** - envia um e-mail com um código de seis dígitos para o seu e-mail alternativo ou o seu e-mail de autenticação (a opção é sua).
    * **Enviar uma mensagem para o meu telemóvel** - envia uma mensagem com um código de seis dígitos para o seu telemóvel ou para o telemóvel de autenticação (a opção é sua).
    * **Ligar para o meu telemóvel** - liga para o seu telemóvel ou para o telemóvel de autenticação (que escolher). Prima a tecla # para verificar a chamada.
    * **Ligar para o meu telefone do escritório** - liga para o seu telefone do escritório. Prima a tecla # para verificar a chamada.
    * **Responder às minhas perguntas de segurança** - apresenta as perguntas de segurança pré-registadas por si para que responda.
4. Preencha os campos obrigatórios com respostas para as informações de teste e clique em **Seguinte**

    ![Verifique os dados de autenticação][Verification]

5. O administrador poderá necessitar de um passo de verificação adicional e poderá ter de repetir o passo 4 novamente com uma opção diferente
6. Na página **Escolher uma nova palavra-passe**, introduza uma nova palavra-passe que cumpra os requisitos de organizações, confirme a palavra-passe e, em seguida, clique em **Concluir**

    ![Alterar a palavra-passe][Change]

7. Quando vir **A palavra-passe foi reposta**, pode iniciar sessão com a nova palavra-passe.

    ![A palavra-passe do utilizador foi reposta][Complete]

> [!NOTE]
> Se o administrador não tiver ativado esta funcionalidade, é apresentada uma ligação "contacte o administrador", para que o administrador possa fornecer assistência por e-mail ou por um próprio portal Web.
>

Depois de utilizar este método para desbloquear ou repor a palavra-passe, poderá receber um e-mail a confirmar que este processo foi concluído e que provém de uma conta como "Microsoft em Nome da Sua Organização Aqui". Se receber um e-mail assim e não utilizou a reposição personalizada de palavra-passe para recuperar o acesso à sua conta, contacte o administrador.

## <a name="change-my-password"></a>Alterar a minha palavra-passe

Se já sabe a palavra-passe e precisa de alterá-la, experimente os passos que se seguem

### <a name="change-your-password-from-the-office-365-portal"></a>Alterar a palavra-passe a partir do portal do Office 365

1. Clique no seu perfil do canto superior direito, clique em **Ver conta**
2. **Segurança e privacidade**
3. **Palavra-passe**
4. Introduza a palavra-passe antiga e defina e confirme a palavra-passe nova
5. **Submeter**

### <a name="change-your-password-from-the-azure-access-panel"></a>Alterar a palavra-passe a partir do Painel de Acesso do Azure

1. Inicie sessão no [Portal de Acesso do Azure](https://myapps.microsoft.com/) através da palavra-passe existente
2. Clique no seu perfil do canto superior direito e clique em **Perfil**
3. **Alterar palavra-passe**
4. Introduza a palavra-passe antiga e defina e confirme a palavra-passe nova
5. **Submeter**

## <a name="next-steps"></a>Passos Seguintes

* [Como se registar para utilizar a reposição personalizada de palavra-passe](active-directory-passwords-reset-register.md)
* [Página de registo de reposição de palavra-passe](http://aka.ms/ssprsetup)
* [Portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Página de início de sessão Não consegue aceder à sua conta?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Verifique os dados de autenticação"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Alterar a palavra-passe"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "A palavra-passe do utilizador foi reposta"

