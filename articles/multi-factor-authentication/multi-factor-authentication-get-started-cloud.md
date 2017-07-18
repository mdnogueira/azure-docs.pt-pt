---
title: "Introdução ao MFA do Azure na nuvem | Microsoft Docs"
description: "Esta é a página do Multi-Factor Authentication do Microsoft Azure que descreve como iniciar o MFA do Azure na nuvem."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 554931e96e073ec2f2f68df2297e1ee21f5eda87
ms.contentlocale: pt-pt
ms.lasthandoff: 04/27/2017

---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Introdução ao Multi-Factor Authentication do Azure na nuvem
Este artigo descreve como começar a utilizar o Multi-Factor Authentication do Azure na nuvem.

> [!NOTE]
> A seguinte documentação fornece informações sobre como ativar utilizadores através do **Portal Clássico do Azure**. Se estiver à procura de informações sobre como configurar o Multi-Factor Authentication do Azure para utilizadores do O365, veja [Configurar a autenticação multifator para o Office 365](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US).

![MFA na Nuvem](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisite"></a>Pré-requisito
[Inscreva-se numa subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/) - Se ainda não possui uma subscrição do Azure, tem de se inscrever numa. Se estiver apenas a começar e a utilizar o MFA do Azure, pode utilizar uma subscrição de avaliação

## <a name="enable-azure-multi-factor-authentication"></a>Ativar a Multi-Factor Authentication do Azure
Desde que os utilizadores tenham licenças que incluam a Multi-Factor Authentication do Azure, não é necessário fazer nada para ativar a MFA do Azure. Pode requerer a verificação de dois passos para cada utilizador. As licenças que permitem a MFA do Azure são:
- Multi-Factor Authentication do Azure
- Azure Active Directory Premium
- Enterprise Mobility + Security

Se não tiver uma destas três licenças ou não tiver suficiente licenças para abranger todos os utilizadores, não há problema. Apenas tem de efetuar um passo extra e [Criar um fornecedor de Multi-Factor Auth](multi-factor-authentication-get-started-auth-provider.md) no diretório.

## <a name="turn-on-two-step-verification-for-users"></a>Ativar a verificação de dois passos para os utilizadores
Para começar a exigir a verificação de dois passos a um utilizador, altere o estado do utilizador de desativado para ativado.  Para obter mais informações sobre os estados do utilizador, veja [User States in Azure Multi-Factor Authentication (Estados do Utilizador no Multi-Factor Authentication do Azure)](multi-factor-authentication-get-started-user-states.md)

Utilize o procedimento seguinte para ativar o MFA para os utilizadores.

### <a name="to-turn-on-multi-factor-authentication"></a>Para ativar a autenticação multifator
1. Inicie sessão no [portal clássico do Azure](https://manage.windowsazure.com) como administrador.
2. No lado esquerdo, clique em **Active Directory**.
3. Em Diretório, selecione o diretório do utilizador que pretende ativar.
   ![Clicar em Diretório](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. Na parte superior, clique em **Utilizadores**.
5. Na parte inferior da página, clique em **Gerir Multi-Factor Auth**. É aberto um novo separador do browser.
   ![Clicar em Diretório](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. Localize o utilizador que pretende ativar para a verificação de dois passos. Pode ter de alterar a vista na parte superior. Verifique se o estado é **desativado.**
   ![Ativar utilizador](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7. Coloque uma **marca de verificação** na caixa junto ao nome.
8. À direita, clique em **Ativar**.
   ![Ativar utilizador](./media/multi-factor-authentication-get-started-cloud/user1.png)
9. Clique em **Ativar Multi-Factor Auth**.
   ![Ativar utilizador](./media/multi-factor-authentication-get-started-cloud/enable2.png)
10. Repare que o estado do utilizador foi alterado de **desativado** para **ativado**.
    ![Ativar Utilizadores](./media/multi-factor-authentication-get-started-cloud/user.png)

Depois de ter ativado os utilizadores, deverá notificá-los por e-mail. Da próxima vez que tentarem iniciar sessão, estes serão solicitados a inscrever a respetiva conta para a verificação de dois passos. Assim que começarem a utilizar a verificação de dois passos, também terão de configurar as palavras-passe das aplicações para evitarem ficar com o acesso bloqueado a aplicações fora do browser.

## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>Utilizar o PowerShell para automatizar a ativação da verificação de dois passos
Para alterar o [estado](multi-factor-authentication-whats-next.md) através do [Azure AD PowerShell](/powershell/azure/overview), pode utilizar o seguinte.  Pode alterar `$st.State` para ser igual a um dos seguintes estados:

* Ativado
* Imposto
* Desativado  

> [!IMPORTANT]
> Não aconselhamos uma alteração direta dos utilizadores do estado Desativado para o estado Imposto. As aplicações não baseadas no browser deixarão de funcionar porque o utilizador não realizou o registo do MFA nem obteve uma [palavra-passe de aplicação](multi-factor-authentication-whats-next.md#app-passwords). Se tiver aplicações não baseadas no browser e precisar de palavras-passe de aplicação, recomenda-se alterar o estado de Desativado para Ativado. Esta alteração permite aos utilizadores registarem e obterem as respetivas palavras-passe de aplicação. Depois disso, pode movê-los para Imposto.

Utilizar o PowerShell é uma opção para ativar utilizadores em massa. Atualmente, não existe nenhuma funcionalidade de ativação em massa no portal do Azure e tem de selecionar cada utilizador individualmente. Se tiver muitos utilizadores, este procedimento poderá ser uma tarefa demorada. Ao criar um script do PowerShell com o procedimento seguinte, poderá percorrer uma lista de utilizadores e ativá-los.

```PowerShell

$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)
Set-MsolUser -UserPrincipalName "bsimon@contoso.com" -StrongAuthenticationRequirements $sta
```

Segue-se um exemplo:

```Powershell
$users = @("bsimon@contoso.com", "jsmith@contoso.com", "ljacobson@contoso.com")
foreach ($user in $users)
{
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
}
```

Para obter mais informações, veja [User states in Azure Multi-Factor Authentication (Estados de utilizador no Multi-Factor Authentication do Azure)](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>Passos Seguintes
Agora que configurou o Multi-Factor Authentication do Azure na nuvem, pode configurar e definir a sua implementação. Veja [Configuring Azure Multi-Factor Authentication (Configurar o Multi-Factor Authentication do Azure)](multi-factor-authentication-whats-next.md) para obter mais detalhes.


