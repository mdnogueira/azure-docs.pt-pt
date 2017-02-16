---
title: "Como as subscrições do Azure estão associadas ao Azure Active Directory | Microsoft Docs"
description: "Iniciar sessão no Microsoft Azure e problemas relacionados, como a relação entre a subscrição do Azure e o Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/01/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e41300ac8f6829ba93ebe7cba6063d82db1119f7


---
# <a name="how-azure-subscriptions-are-associated-with-azure-active-directory"></a>Como as subscrições do Azure estão associadas ao Azure Active Directory
Este artigo inclui informações sobre o início de sessão no Microsoft Azure e os problemas relacionados, como a relação entre a subscrição do Azure e o Azure Active Directory (Azure AD).

## <a name="accounts-that-you-can-use-to-sign-in"></a>Contas que pode utilizar para iniciar sessão
Comecemos pelas contas que pode utilizar para iniciar sessão. Existem dois tipos de conta: uma conta Microsoft (anteriormente conhecida como Microsoft Live ID) e uma conta profissional ou escolar, ou seja uma conta armazenada no Azure AD.

| Conta Microsoft | Conta do Azure AD |
| --- | --- |
| O sistema de identidade do consumidor executado pela Microsoft |O sistema de identidade executado pela Microsoft |
| Autenticação nos serviços orientados para o consumidor, como o Hotmail e o MSN |Autenticação nos serviços orientados para a atividade empresarial, como o Office 365 |
| Os consumidores criam as suas próprias contas Microsoft, tal como quando se inscrevem no e-mail |As empresas e organizações criam e gerem as suas próprias contas profissionais ou escolares |
| São criadas identidades, que serão armazenadas no sistema de contas Microsoft |São criadas identidades com o Azure ou outro serviço, como o Office 365, sendo armazenadas numa instância do Azure AD atribuída à organização |

Embora o Azure permitisse apenas o acesso a utilizadores com contas Microsoft, pode agora ser acedido por utilizadores de *ambos* os sistemas. Isto foi feito ao ter todas as propriedades de fidedignidade do Azure para a autenticação no Azure AD, ter a autenticação de utilizadores organizacionais no Azure AD e ao criar uma relação em que o Azure AD confia no sistema de identidade do consumidor com conta Microsoft para autenticar os utilizadores. Como resultado, o Azure AD consegue autenticar contas “convidadas” da Microsoft, bem como contas “nativas” do Azure AD.

Por exemplo, aqui, um utilizador com uma conta Microsoft, inicia sessão no Portal Clássico do Azure.

> [!NOTE]
> Para iniciar sessão no Portal Clássico do Azure, msmith@hotmail.com tem de ter uma subscrição do Azure. A conta tem de ser de um administrador de serviços ou de um coadministrador da subscrição.
> 
> 

![][1]

Uma vez que este endereço Hotmail é uma conta de consumidor, o sistema de identidade do consumidor com conta Microsoft autentica o início de sessão. O sistema de identidade do Azure AD confia na autenticação efetuada pelo sistema de contas Microsoft e emite um token para aceder aos serviços do Azure.

## <a name="how-an-azure-subscription-is-related-to-azure-ad"></a>Como uma subscrição do Azure esta relacionada ao Azure AD
Cada subscrição do Azure tem uma relação de confiança com uma instância do Azure AD. Tal significa que confia nesse diretório para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório, mas uma subscrição apenas pode confiar num diretório. No separador Definições, pode ver o diretório de confiança da subscrição. Pode [Editar as definições da subscrição](active-directory-understanding-resource-access.md) para alterar o diretório de confiança.

Esta relação de confiança entre uma subscrição e um diretório é diferente da relação de uma subscrição com todos os outros recursos no Azure (sites, bases de dados, e assim sucessivamente), que são mais como recursos subordinados de uma subscrição. Em caso de expiração de uma subscrição, será interrompido o acesso a esses outros recursos associados à subscrição também. No entanto, o diretório permanece no Azure e pode associar outra subscrição a esse diretório e continuar a gerir os utilizadores do diretório.

Do mesmo modo, a extensão do Azure AD que vê na subscrição não funciona como as outras extensões do Portal Clássico do Azure. Outras extensões do Portal Clássico do Azure estão confinadas à subscrição do Azure. O que vê na extensão do Azure AD não varia consoante a subscrição – mostra apenas os diretórios com base no utilizador com sessão iniciada.

Todos os utilizadores têm um diretório raiz único que os autenticam, embora também possam ser convidados noutros diretórios. Na extensão do Azure AD, verá cada diretório a que pertence a conta de utilizador. Não será apresentado qualquer diretório do qual a sua conta não for membro. Um diretório pode emitir tokens para contas profissionais ou escolares no Azure AD ou utilizadores de contas Microsoft (porque o Azure AD é federado pelo sistema de contas Microsoft).

Este diagrama apresenta uma subscrição de Miguel Cardoso depois de se ter inscrito através de uma conta profissional da Contoso.

![][2]

## <a name="how-to-manage-a-subscription-and-a-directory"></a>Como gerir uma subscrição e um diretório
As funções administrativas de uma subscrição do Azure gerem os recursos associados à subscrição do Azure. Estas funções e as melhores práticas de gestão da subscrição são abordadas em [Atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles.md).

Por predefinição, quando se inscreve, é-lhe atribuída a função de Administrador de Serviços. Se outras pessoas tiverem de iniciar sessão e aceder aos serviços com a mesma subscrição, pode adicioná-las como coadministradores. O Administrador de Serviços e coadministradores podem ser contas Microsoft ou contas profissionais ou escolares do diretório ao qual a subscrição do Azure está associada.

O Azure AD tem um conjunto diferente de funções administrativas para gerir o diretório e as funções relacionadas com a identidade. Por exemplo, o administrador global de um diretório pode adicionar utilizadores e grupos no diretório ou exigir o Multifactor Authentication dos utilizadores. É atribuída a função de administrador global a um utilizador que cria um diretório, sendo que poderá atribuir funções de administrador a outros utilizadores.

Tal como com os administradores da subscrição, as funções administrativas do Azure AD podem ser contas Microsoft ou contas profissionais ou escolares. Outros serviços, como o Office 365 e o Microsoft Intune, também recorrem às funções administrativas do Azure AD. Para obter mais informações, consulte [Atribuir funções de administrador](active-directory-assign-admin-roles.md).

No entanto, o que é importante focar é que os administradores da subscrição do Azure e os administradores do diretório do Azure AD são dois conceitos diferentes. Os administradores da subscrição do Azure podem gerir recursos no Azure e podem ver a extensão do Active Directory no Portal Clássico do Azure (porque o Portal Clássico do Azure é um recurso do Azure). Os administradores de diretórios podem gerir propriedades no diretório.

Ambas as funções podem ser atribuídas a uma pessoa, embora tal não seja obrigatório. Pode ser atribuída a função de administrador global do diretório a um utilizador, sem lhe atribuir a função de administrador de serviços ou coadministrador de uma subscrição do Azure. Se não for um administrador da subscrição, este utilizador não poderá iniciar sessão no Portal Clássico do Azure. No entanto, o utilizador poderá executar tarefas de administração de diretórios recorrendo a outras ferramentas, como o Azure AD PowerShell ou o Centro de Administração do Office 365.

## <a name="why-cant-i-manage-the-directory-with-my-current-user-account"></a>Porque não consigo gerir o diretório com a minha conta de utilizador atual?
Por vezes, um utilizador pode tentar iniciar sessão no Portal Clássico do Azure com uma conta profissional ou escolar antes de se inscrever numa subscrição do Azure. Neste caso, o utilizador receberá uma mensagem informando-o de que não existe nenhuma subscrição para essa conta. A mensagem incluirá uma ligação para iniciar uma subscrição de avaliação gratuita.

Depois de se ter inscrito na versão de avaliação gratuita, o utilizador verá o diretório da organização no Portal Clássico do Azure, mas não o poderá gerir (ou seja, não poderá adicionar utilizadores ou editar quaisquer propriedades de utilizador) uma vez que o utilizador não é um administrador global do diretório. A subscrição permite ao utilizador utilizar o Portal Clássico do Azure e ver a extensão do Azure Active Directory. No entanto, são necessárias permissões adicionais de um administrador global para gerir o diretório.

## <a name="using-your-work-or-school-account-to-manage-an-azure-subscription-that-was-created-by-using-a-microsoft-account"></a>Utilizar a conta escolar ou profissional para gerir uma subscrição do Azure criada com uma conta Microsoft
Como melhor prática, deve [Inscrever-se no Azure como organização](sign-up-organization.md) e utilizar uma conta escolar ou profissional para gerir recursos no Azure. Preferem-se contas profissionais ou escolares porque podem ser geridas centralmente pela organização que as emitiu, tendo mais funcionalidades do que as contas Microsoft e sendo diretamente autenticadas pelo Azure AD. A mesma conta fornece acesso a outros serviços online da Microsoft propostos a empresas e organizações, como o Office 365 ou o Microsoft Intune. Se já tiver uma conta que utiliza com essas propriedades, é provável que também queira utilizar essa conta com o Azure. Também já deverá ter uma instância do Active Directory que suporta essas propriedades que a sua subscrição do Azure deverá confiar.

Comparativamente a uma conta Microsoft, também é possível gerir as contas profissionais ou escolares de outras maneiras. Por exemplo, um administrador pode repor a palavra-passe de uma conta profissional ou escolar ou exigir a autenticação multifator.

Em certos casos, pode querer que um utilizador da organização possa gerir recursos associados à uma subscrição do Azure de uma conta Microsoft do consumidor. Para obter mais informações sobre como efetuar a transição para ter várias contas a gerir as subscrições ou diretórios, consulte [Gerir o diretório da subscrição do Office 365 no Azure](#manage-the-directory-for-your-office-365-subscription-in-azure).

## <a name="signing-in-when-you-used-your-work-email-for-your-microsoft-account"></a>Iniciar sessão quando utilizou o endereço de e-mail profissional da conta Microsoft
Se criou um conta Microsoft de consumidor utilizando o seu endereço de e-mail profissional como um identificador de utilizador, ser-lhe apresentada uma página onde poderá selecionar o sistema de Contas do Microsoft Azure ou sistema de Contas Microsoft.

![][3]

Tem contas de utilizador com o mesmo nome, uma no Azure AD e outra no sistema de contas Microsoft de consumidor. Escolha a conta associada à subscrição do Azure que pretende utilizar. Se receber uma mensagem de erro informando que não existe uma subscrição para este utilizador, é provável que tenha escolhido a opção errada. Encerre a sessão e tente novamente. Para obter mais informações sobre os erros que podem impedir o início de sessão, consulte [Resolução de problemas “Não foi possível localizar quaisquer subscrições associadas à sua conta”](https://social.msdn.microsoft.com/Forums/en-US/f952f398-f700-41a1-8729-be49599dd7e2/troubleshooting-we-were-unable-to-find-any-subscriptions-associated-with-your-account-errors-in?forum=windowsazuremanagement).

## <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>Gerir o diretório da subscrição do Office 365 no Azure
Imaginemos que se inscreveu no Office 365 antes de se inscrever no Azure. Agora, pretende gerir o diretório da subscrição do Office 365 no Portal Clássico do Azure. Dependendo se se inscreveu no Azure ou não, este procedimento poderá ser realizado de duas formas.

### <a name="i-do-not-have-a-subscription-for-azure"></a>Não tenho uma subscrição para o Azure
Neste caso, basta [Iniciar sessão no Azure](sign-up-organization.md) com a mesma conta profissional ou escolar utilizada para iniciar sessão no Office 365. As informações relevantes da conta do Office 365 serão pré-provadas no formulário de inscrição do Azure. A função Administrador de Serviços da subscrição será atribuída à conta.  

### <a name="i-do-have-a-subscription-for-azure-using-my-microsoft-account"></a>Subscrevi o Azure com a minha conta Microsoft
Se se inscreveu no Office 365 com uma conta escolar ou profissional e se se inscreveu posteriormente no Azure com uma conta Microsoft, tem dois diretórios: um para o trabalho ou escola e um diretório predefinido criado aquando da inscrição no Azure.

Para gerir ambos os diretórios no Portal Clássico do Azure, siga estes passos.

> [!NOTE]
> Estes passos apenas podem ser realizados quando o utilizador tiver iniciado sessão com uma conta Microsoft. Se o utilizador tiver iniciado sessão com uma conta escolar ou profissional, a opção **Utilizar diretório existente** não estará disponível, porque a conta escolar ou profissional só pode ser autenticada pelo diretório raiz (ou seja, o diretório onde está armazenada a conta escolar ou profissional, que é propriedade da escola ou da empresa).
> 
> 

1. Inicie sessão no Portal Clássico do Azure com a conta Microsoft.
2. Clique em **Novo** > **Serviços de aplicações** > **Active Directory** > **Diretório** > **Criação Personalizada**.
3. Clique em **Utilizar diretório existente**, marque **Estou pronto para encerrar a sessão agora** e clique na marca de verificação para concluir a ação.
4. Inicie sessão no Portal Clássico do Azure com uma conta com direitos de administrador global do diretório profissional ou escolar.
5. Quando aparecer **Utilizar o diretório da Contoso com o Azure?**, clique em **Continuar**.
6. Clique em **Terminar sessão agora**.
7. Inicie uma nova sessão no Portal Clássico do Azure com a conta Microsoft. Ambos os diretórios aparecem na extensão do Active Directory.

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre como alterar os administradores para uma subscrição do Azure, consulte [Como adicionar ou alterar funções de administrador do Azure](../billing-add-change-azure-subscription-administrator.md)
* Para saber mais sobre como o Microsoft Azure controla o acesso aos recursos, consulte [Noções sobre o acesso aos recursos no Azure](active-directory-understanding-resource-access.md)
* Para obter mais informações sobre como atribuir funções no Azure AD, consulte [Atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles.md)
* [Inscrever-se no Azure como uma organização](sign-up-organization.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG



<!--HONumber=Dec16_HO1-->


