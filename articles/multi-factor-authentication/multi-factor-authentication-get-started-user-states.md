---
title: Estados de utilizador do Microsoft Azure multi-factor Authentication
description: Saiba mais sobre os Estados de utilizador no Azure MFA.
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 6a0f8cb76684a6efcc5e2d4be05493f18d5d4c76
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>Como requerer a verificação de dois passos para um utilizador ou grupo

Existem duas abordagens para exigir a verificação de dois passos. A primeira opção consiste em ativar cada utilizador individual para o Azure multi-factor Authentication (MFA). Quando os utilizadores estão ativados individualmente, sempre efetuar verificação de dois passos (com algumas exceções, como quando iniciam sessão de endereços IP fidedignos ou se a funcionalidade de dispositivos memorizados está ativada). A segunda opção consiste em configurar uma política de acesso condicional que requer a verificação de dois passos em determinadas condições.

>[!TIP] 
>Escolha um dos seguintes métodos para exigir verificação de dois passos, não ambos. Ativar um utilizador para o MFA do Azure substitui quaisquer políticas de acesso condicional.

## <a name="which-option-is-right-for-you"></a>Qual é a opção é adequada para si

**Ativar a MFA do Azure, alterando os Estados de utilizador** é a abordagem tradicional para exigir a verificação de dois passos. Funciona para o Azure MFA na nuvem e o servidor MFA do Azure. Todos os utilizadores que ativar tem a mesma experiência, que consiste em efetuar verificação de dois passos, sempre iniciar sessão. Ativar um utilizador substitui quaisquer políticas de acesso condicional que podem afetar esse utilizador. 

**Ativar a MFA do Azure com uma política de acesso condicional** é uma abordagem mais flexível para exigir a verificação de dois passos. -Funcionar apenas para o Azure MFA na nuvem, no entanto, e o acesso condicional é um [paga funcionalidade do Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features). Pode criar políticas de acesso condicional que se aplicam a grupos, bem como os utilizadores individuais. Grupos de alto risco podem ser especificados mais restrições de grupos de baixo risco, ou pode ser necessário apenas para aplicações na nuvem de alto risco e ignorada para baixo risco aqueles verificação em dois passos. 

Ambas estas opções de pedem aos utilizadores para se registar para Azure multi-factor Authentication na primeira vez que iniciar sessão depois de ativassem os requisitos. Ambas as opções também funcionam com o configuráveis [definições de multi-factor Authentication do Azure](multi-factor-authentication-whats-next.md)

## <a name="enable-azure-mfa-by-changing-user-status"></a>Ativar a MFA do Azure ao alterar o estado do utilizador

Contas de utilizador no multi-factor Authentication do Azure tem os seguintes três Estados distintos:

| Estado | Descrição | Aplicações não baseadas no browser afetadas |
|:---:|:---:|:---:|
| Desativado |O estado predefinido para um novo utilizador não inscrito Azure multi-factor Authentication (MFA). |Não |
| Ativado |O utilizador inscreveu na MFA do Azure, mas não registado. Serão solicitados para registar da próxima vez que iniciar sessão. |Não.  Podem continuar a funcionar até que o processo de registo é concluído. |
| Imposto |O utilizador foi inscrito e concluiu o processo de registo para o MFA do Azure. |Sim.  As aplicações necessitam de palavras-passe de aplicação. |

Estado do utilizador reflete se um administrador inscreveu-los na MFA do Azure e, se poderem concluir o processo de registo.

Todos os utilizadores começam *desativada*. Aquando da inscrição de utilizadores na MFA do Azure, as alterações de estado *ativada*. Quando os utilizadores ativados iniciar e concluir o processo de registo, o estado muda para *imposta*.  

### <a name="view-the-status-for-a-user"></a>Ver o estado de um utilizador

Utilize os seguintes passos para aceder à página onde pode ver e gerir os Estados de utilizador:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Aceda a **do Azure Active Directory** > **utilizadores e grupos** > **todos os utilizadores**.
3. Selecione **multi-factor Authentication**.
   ![Selecionar a autenticação Multifator](./media/multi-factor-authentication-get-started-user-states/selectmfa.png)
4. Uma nova página que apresenta os Estados de utilizador, é aberto.
   ![Estado de utilizador do multi-factor authentication - captura de ecrã](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Alterar o estado de um utilizador

1. Utilize os passos anteriores para obter a página de utilizadores de autenticação multifator.
2. Localize o utilizador que pretende ativar a MFA do Azure. Pode ter de alterar a vista na parte superior. 
   ![Localizar utilizador - captura de ecrã](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. Selecione a caixa junto do respetivo nome.
4. À direita, em passos rápidos, escolha **ativar** ou **desativar**.
   ![Ativar o utilizador selecionado - captura de ecrã](./media/multi-factor-authentication-get-started-cloud/user1.png)

   >[!TIP]
   >*Ativado* utilizadores mudar automaticamente para *imposta* quando de registo para o MFA do Azure. Não deve alterar manualmente o estado do utilizador imposta. 

5. Confirme a sua selecção na janela de pop-up que é aberta. 

Depois de ativar os utilizadores, deve notificá-los por correio eletrónico. Informe-o de que irá ser pedidos para registar da próxima vez que iniciar sessão. Além disso, se a organização utilizar aplicações não baseadas no browser que não suportem a autenticação moderna, estes terá de criar palavras-passe de aplicação. Também pode incluir uma hiperligação para o nosso [Guia do utilizador final do MFA do Azure](./end-user/multi-factor-authentication-end-user.md) para ajudar a começar a utilizar.

### <a name="use-powershell"></a>Utilizar o PowerShell
Para alterar o estado de utilizador Estado utilizando [Azure AD PowerShell](/powershell/azure/overview), alterar `$st.State`. Existem três Estados possíveis:

* Ativado
* Imposto
* Desativado  

Não mova os utilizadores diretamente para o *imposto* estado. As aplicações não baseadas no browser deixarão de funcionar porque o utilizador não realizou o registo do MFA nem obteve uma [palavra-passe de aplicação](multi-factor-authentication-whats-next.md#app-passwords). 

Com o PowerShell é uma boa opção quando pretender ativar utilizadores em massa. Crie um script do PowerShell que repete através de uma lista de utilizadores e permite-lhes:

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Segue-se um exemplo:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>Ativar a MFA do Azure com uma política de acesso condicional

Acesso condicional é uma funcionalidade paga do Azure Active Directory, com muitas opções de configuração possíveis. Estes passos guiá-lo através de uma forma de criar uma política. Para obter mais informações, leia sobre [acesso condicional no Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Aceda a **do Azure Active Directory** > **acesso condicional**.
3. Selecione **nova política**.
4. Em **atribuições**, selecione **utilizadores e grupos**. Utilize o **incluir** e **excluir** separadores para especificar quais os utilizadores e grupos serão geridos pela política.
5. Em **atribuições**, selecione **aplicações em nuvem**. Optar por incluir **todas as aplicações em nuvem**.
6. Em **controlos de acesso**, selecione **conceder**. Escolha **requer autenticação multifator**.
7. Ativar **ativar política de** para **no** e, em seguida, selecione **guardar**.

As outras opções na política de acesso condicional permitem-lhe especificar exatamente quando a verificação deverá ser necessária. Por exemplo, pode efetuar uma política de Estados: quando contratantes tentam aceder à nossa aplicação de aprovisionamento de redes não fidedignas em dispositivos que não estejam associados a um domínio, exigir verificação de dois passos. 

## <a name="next-steps"></a>Passos seguintes

- Obter sugestões sobre o [melhores práticas para acesso condicional](../active-directory/active-directory-conditional-access-best-practices.md)

- Gerir definições de multi-factor Authentication para [os seus utilizadores e os respetivos dispositivos](multi-factor-authentication-manage-users-and-devices.md)