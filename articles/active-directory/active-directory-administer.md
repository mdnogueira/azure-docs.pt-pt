---
title: "Descrição geral de como utilizar um diretório de inquilinos do Azure Active Directory | Microsoft Docs"
description: "Explica o que é um inquilino do Azure AD e como gerir o Azure com o Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro;oldportal
ms.openlocfilehash: 85e24587f07a4dc65a2f116499f3f7e00c3ac2b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-azure-ad-directory"></a>Gerir o seu diretório do Azure AD

## <a name="what-is-an-azure-ad-tenant"></a>O que é um inquilino do Azure AD?
No Azure Active Directory (Azure AD), um inquilino é uma instância dedicada de um diretório do Azure AD que a sua organização recebe quando se inscreve num serviço cloud da Microsoft, como o Azure ou o Office 365. Cada diretório do Azure AD é distinto e separado de outros diretórios do Azure AD. Tal como um edifício de escritórios empresariais é um recurso seguro específico apenas para a sua organização, um diretório do Azure AD também foi concebido para ser um recurso com utilização segura apenas para a sua organização. A arquitetura do Azure AD isola os dados do cliente e as informações de identidade para que os utilizadores e administradores de um diretório do Azure AD não possa aceder aos dados de outro diretório de forma acidental ou intencional.

![Gerir o Azure Active Directory](./media/active-directory-administer/aad_portals.png)

## <a name="how-can-i-get-an-azure-ad-directory"></a>Como posso obter um diretório do Azure AD?
O Azure AD oferece as principais funcionalidades de gestão de diretórios e de identidades por detrás da maioria dos Cloud Services da Microsoft, incluindo:

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Pode obter um diretório do Azure AD quando se inscreve para qualquer um destes serviços cloud da Microsoft. Pode criar diretórios adicionais, conforme necessário. Por exemplo, poderá manter o primeiro diretório como um diretório de produção e criar posteriormente outro diretório para o processo de teste e transição.

### <a name="using-the-azure-ad-directory-that-comes-with-a-new-azure-subscription"></a>Utilizar o diretório do Azure AD que vem incluído com as subscrições do Azure novas

Recomendamos que utilize a conta de administrador que utilizou no seu primeiro serviço quando se inscreveu noutros serviços Microsoft. As informações que indicar da primeira vez que se inscrever num serviço Microsoft são utilizadas para criar uma instância de diretório do Azure AD nova para a sua organização. Se utilizar este diretório para autenticar as tentativas de início de sessão quando subscrever outros serviços Microsoft, estes podem utilizar as contas de utilizador, as políticas, as definições ou a integração de diretórios no local que já tenha configurado no diretório predefinido.

Por exemplo, se se inscrever numa subscrição do Microsoft Intune e, posteriormente, sincronizar o seu Active Directory no local com o seu diretório do Azure Directory, pode inscrever-se noutro serviço Microsoft, como o Office 365, e beneficiar, facilmente, das mesmas vantagens de integração de diretórios que já tem com o Microsoft Intune.

Para obter mais informações acerca da integração do seu diretório no local com o Azure AD, veja [Integração de diretórios com o Azure AD Connect](active-directory-aadconnect.md).

### <a name="associate-an-existing-azure-ad-directory-with-a-new-azure-subscription"></a>Associar um diretório do Azure AD existente a uma nova subscrição do Azure
Pode associar uma nova subscrição do Azure ao mesmo diretório que autentica o início de sessão de uma subscrição já existente do Office 365 ou do Microsoft Intune. Para obter mais informações sobre este cenário, veja [Transfer ownership of an Azure subscription to another account](../billing/billing-subscription-transfer.md) (Transferir a propriedade de uma subscrição do Azure para outra conta)

### <a name="create-an-azure-ad-directory-by-signing-up-for-a-microsoft-cloud-service-as-an-organization"></a>Criar um diretório do Azure AD ao inscrever-se num serviço em nuvem da Microsoft como uma organização
Se ainda não tiver uma subscrição de um serviço cloud da Microsoft, pode utilizar uma das ligações abaixo para se inscrever. A inscrição no primeiro serviço cria automaticamente um diretório do Azure AD.

* [Microsoft Azure](https://account.azure.com/organization)
* [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
* [Microsoft Intune](https://portal.office.com/Signup/Signup.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&dl=INTUNE_A&ali=1#0%20)

### <a name="how-to-change-the-default-directory-for-a-subscription"></a>Como alterar o diretório predefinido de uma subscrição

1. Inicie sessão no [Centro de Contas do Azure](https://account.azure.com/Subscriptions) com uma conta que seja o Administrador de Conta da subscrição para transferir a propriedade da subscrição.
2. Confirme que o utilizador que pretende que seja o proprietário da subscrição está no diretório segmentado.
3. Clique em **Transferir subscrição**.
4. Especifique o destinatário. O destinatário recebe automaticamente um e-mail com uma ligação de aceitação.
5. O destinatário clica na ligação e segue as instruções, o que inclui a introdução das respetivas informações de pagamento. Quando o destinatário concluir o processo com êxito, a subscrição é transferida. 
6. O diretório predefinido da subscrição é alterado para o diretório em que o utilizador se encontra, caso a transferência de propriedade de subscrição tenha sido efetuada com êxito.

Para saber mais, consulte [Transferir propriedade da subscrição do Azure para outra conta](../billing/billing-subscription-transfer.md)

### <a name="manage-the-default-directory-in-azure"></a>Gerir o diretório predefinido no Azure
Quando se inscreve no Azure, é associado à sua subscrição um diretório predefinido do Azure AD. A utilização do Azure AD não tem custos e os seus diretórios são um recurso gratuito. Existem serviços pagos do Azure AD, os quais estão licenciados em separado e oferecem funcionalidades adicionais, como imagem corporativa da empresa no início de sessão e a reposição de palavras-passe personalizada. Também pode criar um domínio personalizado com um nome DNS de que é proprietário em vez do domínio *.com predefinido.

## <a name="how-can-i-manage-directory-data"></a>Como posso gerir os dados do diretório?
Para administrar de uma ou mais subscrições do serviço cloud da Microsoft, pode utilizar o [centro de administração do Azure AD](https://aad.portal.azure.com), o portal de contas do Microsoft Intune ou o [Centro de Administração do Office 365](https://portal.office.com/) para gerir os dados dos diretórios da sua organização. Também pode utilizar os [cmdlets do PowerShell do Azure Active Directory](https://docs.microsoft.com/powershell/azure/active-directory)para o ajudar a gerir os dados armazenados no Azure AD.

Num dos portais seguintes (ou cmdlets), pode:

* Criar e gerir contas de utilizador e de grupo
* Gerir serviços cloud associados às subscrições da sua organização
* Configurar a integração no local com os serviços de identidade e autenticação do Azure AD

O centro de administração do Azure AD, o Centro de Administração do Office 365, o portal de contas do Microsoft Intune e os cmdlets do Azure AD leem e escrevem numa única instância partilhada do Azure AD que está associada ao diretório da sua organização. Cada uma destas ferramentas funciona como uma interface de front-end que extrai ou altera os dados do diretório.

Quando utiliza um dos portais ou dos cmdlets para alterar os dados da sua organização enquanto tem sessão iniciada no contexto de um destes serviços, as alterações também aparecem nos outros portais da próxima vez que iniciar sessão. Estes dados são partilhados entre os serviços cloud da Microsoft que tem subscritos.

Por exemplo, se utilizar o Centro de Administração do Office 365 para impedir um utilizador de iniciar sessão, a ação impede-o de iniciar sessão em qualquer outro serviço que a sua organização tenha subscrito. Se vir a mesma conta de utilizador no portal de contas do Microsoft Intune, verá também que o utilizador está bloqueado.

## <a name="how-can-i-add-and-manage-multiple-directories"></a>Como posso adicionar e gerir múltiplos diretórios?
Pode [adicionar um diretório do Azure AD no portal do Azure](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Preencha as informações e selecione **Criar**.

Pode gerir cada diretório como um recurso totalmente independente: cada diretório é um par, completo e logicamente independente dos outros diretórios que gere; não existe nenhuma relação principal-subordinado entre diretórios. Esta independência entre diretórios inclui independência de recursos, independência administrativa e independência de sincronização.

* **Independência de recursos**. Se criar ou eliminar um recurso num diretório, isso não tem impacto em nenhum outro recurso de nenhum outro diretório, com a exceção parcial dos utilizadores externos. Se utilizar um domínio personalizado “contoso.com” com um diretório, não o poderá utilizar com qualquer outro diretório.
* **Independência administrativa**.  Se um utilizador que não seja administrador do diretório “Contoso” criar um diretório de “Teste”, então:
  
  * Os administradores do diretório “Contoso” não têm privilégios administrativos diretos para o diretório de “Teste”, exceto se um administrador de “Teste” lhes conceder esses privilégios. Os administradores de “Contoso” podem controlar o acesso ao diretório “Teste”, uma vez que têm controlo sobre a conta de utilizador que criou “Teste”.
    
  * Se atribuir ou remover uma função de administrador de um utilizador num diretório, a alteração não afeta nenhuma função de administrador que o utilizador possa ter em qualquer outro diretório.
* **Independência de sincronização**. Pode configurar cada inquilino do Azure AD de forma independente para que os dados se sincronizem a partir de uma instância única com a ferramenta de sincronização de diretórios do Azure AD Connect.

Ao contrário de outros recursos do Azure, os seus diretórios não são recursos subordinados de uma subscrição do Azure. Por isso, se cancelar ou deixar que a subscrição do Azure expire, pode continuar a aceder aos dados do diretório através do Azure AD PowerShell, da Graph API do Azure ou de outras interfaces, como o Centro de Administração do Office 365. Também pode associar outra subscrição ao diretório.

## <a name="how-to-prepare-to-delete-an-azure-ad-directory"></a>Como preparar a eliminação de um diretório do Azure AD
Um administrador global pode eliminar um diretório do Azure AD a partir do portal. Quando um diretório é eliminado, todos os recursos contidos no mesmo também o são. Confirme que não precisa do diretório antes de o eliminar.

> [!NOTE]
> Se o utilizador tiver iniciado sessão com uma conta profissional ou escolar, não deverá tentar eliminar o diretório raiz dele. Por exemplo, se o utilizador tiver iniciado sessão como joe@contoso.onmicrosoft.com, esse utilizador não poderá eliminar o diretório contoso.onmicrosoft.com, uma vez que se trata do domínio predefinido.

O Azure AD requer que certas condições sejam cumpridas para eliminar um diretório. Isto reduz o risco de eliminar um diretório que poderia ter um impacto negativo sobre os utilizadores ou aplicações, como a capacidade de os utilizadores iniciarem sessão no Office 365 ou acederem a recursos no Azure. Por exemplo, se um diretório de uma subscrição for eliminado involuntariamente, os utilizadores não poderão aceder aos recursos do Azure dessa subscrição.

Verificam-se as seguintes condições:

* O único utilizador no diretório deve ser o administrador global que o vai eliminar. Deverão ser eliminados todos os outros utilizadores antes de eliminar o diretório. Se os utilizadores forem sincronizados no local, a sincronização tem de ser desativada e os utilizadores têm de ser eliminados do diretório na cloud através do portal do Azure ou dos cmdlets do Azure PowerShell. Não é necessário eliminar grupos ou contactos, tais como contactos adicionados a partir do Centro de Administração do Office 365.
* Não podem existir aplicações no diretório. As aplicações devem ser eliminadas antes de eliminar o diretório.
* Não podem ser associados ao diretório nenhuns fornecedores multi-factor authentication.
* Não podem existir subscrições para quaisquer Serviços Online da Microsoft, como o Microsoft Azure, o Office 365 ou o Azure AD Premium, associadas ao diretório. Por exemplo, se tiver criado um diretório predefinido no Azure, não o poderá eliminar se a sua subscrição do Azure ainda depender desse diretório para a autenticação. Do mesmo modo, não pode eliminar um diretório se outro utilizador tiver associado uma subscrição ao mesmo. 


## <a name="next-steps"></a>Passos seguintes
* [Fórum do Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
* [Fórum do Multi-Factor Authentication do Azure](https://social.msdn.microsoft.com/Forums/home?forum=windowsazureactiveauthentication)
* [Stack Overflow for Azure questions](http://stackoverflow.com/questions/tagged/azure) (Perguntas do Stack Overflow para Azure)
* [Azure Active Directory do PowerShell](https://docs.microsoft.com/powershell/azure/active-directory)
* [Atribuir funções de administrador no Azure AD](active-directory-assign-admin-roles.md)
