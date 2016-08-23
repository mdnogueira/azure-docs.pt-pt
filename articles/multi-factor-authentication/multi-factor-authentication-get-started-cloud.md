<properties 
    pageTitle="Introdução ao Multi-Factor Authentication do Microsoft Azure na nuvem" 
    description="Esta é a página do Multi-Factor Authentication do Microsoft Azure que descreve como iniciar o MFA do Azure na nuvem." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtand"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>

# Introdução ao Multi-Factor Authentication do Azure na nuvem
No seguinte artigo, irá aprender a começar a utilizar o Multi-Factor Authentication do Azure na nuvem.

> [AZURE.NOTE]  A seguinte documentação fornece informações sobre como ativar utilizadores através do **Portal Clássico do Azure**. Se estiver à procura de informações sobre como configurar o Multi-Factor Authentication do Azure para utilizadores do O365, veja [Configurar o Multi-Factor Authentication para o Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![MFA na Nuvem](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## Pré-requisitos
São necessários os seguintes pré-requisitos antes de poder ativar o Multi-Factor Authentication do Azure para os utilizadores. 




- [Inscreva-se numa subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/) - Se ainda não possui uma subscrição do Azure, tem de se inscrever numa. Se estiver apenas a começar e a utilizar o MFA do Azure, pode utilizar uma subscrição de avaliação
2. [Crie um Fornecedor de Multi-Factor Auth](multi-factor-authentication-get-started-auth-provider.md) e atribua-o ao diretório ou [atribua licenças a utilizadores](multi-factor-authentication-get-started-assign-licenses.md) 

> [AZURE.NOTE]  As licenças estão disponíveis para os utilizadores que têm o MFA do Azure, o Azure AD Premium ou o Enterprise Mobility Suite (EMS).  O MFA está incluído no Azure AD Premium e no EMS. Se tiver licenças suficientes, não é necessário criar um Fornecedor de Autenticação. 
        

## Ativar a autenticação multifator para os utilizadores
Para ativar a autenticação multifator para um utilizador, altere simplesmente o estado do utilizador de desativado para ativado.  Para obter mais informações sobre os estados de utilizador, veja [Estados de Utilizador no Multi-Factor Authentication do Azure](multi-factor-authentication-get-started-user-states.md)

Utilize o procedimento seguinte para ativar o MFA para os utilizadores.

### Para ativar a autenticação multifator
--------------------------------------------------------------------------------
1.  Inicie sessão no **Portal clássico do Azure** como Administrador.
2.  No lado esquerdo, clique em **Active Directory**.
3.  Em **Diretório**, clique no diretório para o utilizador que pretende ativar.
![Clique em Diretório](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Na parte superior, clique em **Utilizadores**.
5.  Na parte inferior da página, clique em **Gerir Multi-Factor Auth**.
![Clique em Diretório](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Esta ação irá abrir um novo separador do browser.  Localize o utilizador que pretende ativar para a autenticação multifator. Pode ter de alterar a vista na parte superior. Certifique-se de que o estado é **desativado.**
![Ativar utilizador](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Coloque uma **marca de verificação** na caixa junto ao nome.
7.  À direita, clique em **Ativar**. 
![Ativar utilizador](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Clique em **Ativar Multi-Factor Auth**.
![Ativar utilizador](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Deve reparar que o estado do utilizador foi alterado de **desativado** para **ativado**.
![Ativar Utilizadores](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  Depois de ter ativado os utilizadores, é recomendado notificá-los por e-mail.  Deve também informá-los sobre como podem utilizar as aplicações fora do browser para evitarem ser bloqueados.


## Automatizar a ativação da autenticação multifator com o PowerShell

Para alterar o [estado](multi-factor-authentication-whats-next.md) através do [Azure AD PowerShell](../powershell-install-configure.md), pode utilizar o seguinte.  Pode alterar `$st.State` para ser igual a um dos seguintes estados:


- Ativado
- Imposto
- Desativado  

> [AZURE.IMPORTANT]  Tenha em atenção que se passar diretamente do estado Desativado para o estado Imposto, os clientes de autenticação não moderna deixarão de funcionar, porque o utilizador não passou pelo registo do MFA nem obteve uma [palavra-passe de aplicação](multi-factor-authentication-whats-next.md#app-passwords).  Se tiver clientes de autenticação não moderna e exigir palavras-passe de aplicação, é recomendado passar do estado Desativado para Ativado.  Isto permitirá aos utilizadores registar e obter as respetivas palavras-passe de aplicação.   
        
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Utilizar o PowerShell é uma opção para ativar utilizadores em massa.  Atualmente, não existe nenhuma funcionalidade de ativação em massa no portal do Azure e tem de selecionar cada utilizador individualmente.  Isto pode ser uma tarefa demorada se tiver muitos utilizadores.  Ao criar um script do PowerShell utilizando o procedimento acima, pode percorrer uma lista de utilizadores e ativá-los.  Segue-se um exemplo:
    
    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Para obter mais informações sobre os estados de utilizador, veja [Estados de Utilizador no Multi-Factor Authentication do Azure](multi-factor-authentication-get-started-user-states.md)

## Passos Seguintes
Agora que configurou a autenticação multifator na nuvem, pode configurar a sua implementação.  Veja [Configurar o Multi-Factor Authentication do Azure.]



<!--HONumber=Aug16_HO1-->


