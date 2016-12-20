---
title: "Administrar o diretório do Azure AD | Microsoft Docs"
description: "Explica o que é um inquilino do Azure AD e como gerir o Azure através do Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
writer: markvi
manager: femila
editor: 
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/10/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 001ffc0f9c7465552392a9848ef1487a4c0eafce


---
# <a name="administer-your-azure-ad-directory"></a>Administrar o diretório do Azure AD
## <a name="what-is-an-azure-ad-tenant"></a>O que é um inquilino do Azure AD?
Na área de trabalho física, o termo inquilino pode ser definido como um grupo ou uma empresa que ocupa um edifício. Por exemplo, a sua organização poderá ser a proprietária de um escritório num edifício. Esse edifício poderá encontrar-se numa rua com várias outras organizações. A sua organização será considerada como inquilino desse edifício. Esse edifício é um recurso da sua organização que fornece segurança e garante-lhe a possibilidade de fazer negócios em segurança. Está também separado dos outros negócios na sua rua. Tal garante que a organização e respetivos recursos estão isolados de outras organizações.

Na área de trabalho ativada na nuvem, um inquilino pode ser definido com um cliente ou uma organização que possui e gere uma instância específica do referido serviço em nuvem. Com a plataforma de identidade fornecida pelo Microsoft Azure, um inquilino não é mais do que uma instância dedicada do Azure Active Directory (Azure AD) que a sua organização recebe e possui quando se inscreve num serviço em nuvem da Microsoft, como o Azure ou o Office 365.

Cada diretório do Azure AD é distinto e separado de outros diretórios do Azure AD. Tal como um edifício de escritórios empresariais é um recurso seguro específico apenas para a sua organização, um diretório do Azure AD também foi concebido para ser um recurso com utilização segura apenas para a sua organização. A arquitetura do Azure AD impede que os dados do cliente e as informações de identidade se misturem. Isto significa que os utilizadores e administradores de um diretório do Azure AD não podem, acidental ou intencionalmente, aceder aos dados de outro diretório.

![Gerir o Azure Active Directory][1]

## <a name="how-can-i-get-an-azure-ad-directory"></a>Como posso obter um diretório do Azure AD?
O Azure AD oferece as principais funcionalidades de gestão de diretórios e de identidades por detrás da maioria dos Cloud Services da Microsoft, incluindo:

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Obtém um diretório do Azure AD quando se inscreve em qualquer um desses Cloud Services da Microsoft. Pode criar diretórios adicionais, conforme necessário. Por exemplo, poderá manter o primeiro diretório como um diretório de produção e criar posteriormente outro diretório para o processo de teste e transição.

> [!NOTE]
> Depois de se inscrever no primeiro serviço, recomendamos que utilize a mesma conta de administrador associada à sua organização sempre que se inscrever noutros Cloud Services da Microsoft.
> 
> 

Na primeira vez que se inscrever num serviço em nuvem da Microsoft, ser-lhe-á pedido que faculte os detalhes da sua organização, bem como o nome de registo do domínio na Internet. Estas informações são posteriormente utilizadas para criar uma nova instância do diretório do Azure AD para a sua organização. Esse mesmo diretório é utilizado para autenticar as tentativas de início de sessão, caso subscreva vários Cloud Services da Microsoft.

Os serviços adicionais permitem-lhe tirar o maior partido de quaisquer contas de utilizador, políticas, definições existentes ou integração de diretórios no local que configurou para ajudar a melhorar a eficiência entre a infraestrutura da identidade no local da organização e o Azure AD.

Por exemplo, se inicialmente tiver assinado uma subscrição do Microsoft Intune e concluído os passos necessários para integrar o Active Directory no local ao diretório do Azure AD através da implementação da sincronização de diretórios e/ou servidores de início de sessão único, poderá subscrever outro serviço em nuvem da Microsoft, como o Office 365, que também pode potenciar a integração do mesmo diretório que utiliza com o Microsoft Intune.

Para obter mais informações acerca da integração do diretório no local com o Azure AD, consulte o artigo [Integração de diretórios](active-directory-aadconnect.md).

### <a name="associate-an-azure-ad-directory-with-a-new-azure-subscription"></a>Associar um diretório do Azure AD a uma nova subscrição do Azure
Pode associar uma nova subscrição do Azure ao mesmo diretório que autentica o início de sessão de uma subscrição existente do Office 365 ou do Microsoft Intune. Inicie sessão no Portal de Gestão do Azure com a conta escolar ou profissional. O Portal de Gestão devolve uma mensagem indicando que não foi possível localizar subscrições para essa conta. Selecione **Iniciar Sessão no Azure**. O diretório ficará disponível para a administração no portal. Para obter mais informações, consulte [Gerir o diretório da subscrição do Office 365 no Azure](active-directory-how-subscriptions-associated-directory.md#manage-the-directory-for-your-office-365-subscription-in-azure).

Para obter um vídeo com as perguntas comuns de utilização do Azure AD, consulte [Azure Active Directory – perguntas comuns sobre a utilização da inscrição e do início de sessão](http://channel9.msdn.com/Series/Windows-Azure-Active-Directory/WAADCommonSignupsigninquestions).

### <a name="create-an-azure-ad-directory-by-signing-up-for-a-microsoft-cloud-service-as-an-organization"></a>Criar um diretório do Azure AD ao inscrever-se num serviço em nuvem da Microsoft como uma organização
Se ainda não tiver uma subscrição do serviço em nuvem da Microsoft, utilize uma das ligações abaixo para se inscrever. Aquando da inscrição ao primeiro serviço, será automaticamente criado um diretório do Azure AD.

* [Microsoft Azure](https://account.windowsazure.com/organization)
* [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
* [Microsoft Intune](https://account.manage.microsoft.com/Signup/MainSignUp.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&ali=1)

### <a name="manage-an-azure-provisioned-default-directory"></a>Gerir um diretório Predefinido aprovisionado pelo Azure
Hoje em dia, um diretório é automaticamente criado quando se inscreve no Azure, sendo a subscrição associada a esse diretório. No entanto, não foi automaticamente criado nenhum diretório se se inscreveu no Azure antes de outubro de 2013. Nesse caso, o Azure pode ter “preenchido” a sua conta, facultando um diretório Predefinido. Em seguida, a subscrição foi associada a esse diretório Predefinido.

O preenchimento dos diretórios foi realizado em outubro de 2013, como parte de uma melhoria geral do modelo de segurança do Azure. Isso ajuda a oferecer funcionalidades de identidade organizacional a todos os clientes do Azure, garantindo ainda que todos os recursos do Azure são acedidos no contexto de um utilizador no diretório. Não pode utilizar o Azure sem um diretório. Para tal, qualquer utilizador que se tenha inscrito antes do 7 de julho de 2013 e não possui um diretório, terá de criar um. Se já tiver criado um diretório, a subscrição foi associada a esse diretório.

A utilização do Azure AD é gratuita. O diretório é um recurso gratuito. Está disponível um escalão adicional, o Azure Active Directory Premium, licenciado à parte e que fornece funcionalidades adicionais, tais como imagem corporativa e reposição personalizada de palavra-passe.

Para alterar o nome a apresentar do diretório, clique no diretório no portal e, em seguida, em **Configurar**. Conforme explicado posteriormente neste tópico, pode adicionar um novo diretório ou eliminar um diretório que já não necessita. Para associar a sua subscrição a um diretório diferente, clique na extensão **Definições** no painel de navegação esquerdo e, na parte inferior da página **Subscrições**, clique em **Editar Diretório**. Em vez do domínio predefinido *.onmicrosoft.com, pode criar um domínio personalizado com um nome DNS que registou, o que poderá ser preferível com um serviço como o SharePoint Online.

## <a name="how-can-i-manage-directory-data"></a>Como posso gerir os dados do diretório
Como administrador de uma ou mais subscrições do serviço em nuvem da Microsoft, pode utilizar o Portal de Gestão do Azure, o portal de contas do Microsoft Intune ou o Centro de Administração do Office 365 para gerir os dados do diretório das suas organizações. Também pode transferir e executar os cmdlets do [Módulo Microsoft Azure Active Directory para Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) para ajudar a gerir os dados armazenados no Azure AD.

utilizando um destes portais (ou cmdlets), pode:

* Criar e gerir contas de utilizador e de grupo
* Gerir Cloud Services associados subscritos pela sua organização
* Configurar a integração no local com o serviço de diretório

O Portal de Gestão do Azure, o Centro de Administração do Office 365, o portal de contas do Microsoft Intune e os cmdlets do Azure AD leem e escrevem para única instância partilhada do Azure AD associada ao diretório da sua organização, conforme ilustrados a seguir.  Desta forma, os portais (ou cmdlets) agem como uma interface de front-end que solicita e/ou modifica os dados do diretório.

![][2]

Estes portais de conta e cmdlets do Azure AD PowerShell associados utilizados para gerir utilizadores e grupos são criados sobre a plataforma do Azure AD.

Quando alterar os dados das organizações através de um dos portais (ou cmdlets) enquanto tiver sessão iniciada no âmbito de um desses serviços, a alteração também será mostrada nos outros portais da próxima vez que iniciar sessão no âmbito daquele serviço uma vez que esses dados são partilhados entre os Cloud Services da Microsoft que tiver subscrito.
Por exemplo, se tiver utilizado o Centro de Administração do Office 365 para impedir um utilizador de iniciar sessão, a ação irá bloquear o utilizador de iniciar sessão em qualquer outro serviço subscrito pela sua organização. Se tiver de parar a conta desse mesmo utilizador no âmbito do portal de contas do Microsoft Intune, poderá ver que o utilizador está bloqueado.

## <a name="how-can-i-add-and-manage-multiple-directories"></a>Como posso adicionar e gerir múltiplos diretórios?
Pode adicionar um diretório do Azure AD no Portal de Gestão do Azure. Selecione a extensão **Active Directory** à esquerda e clique em **Adicionar**.

Pode gerir cada diretório como um recurso totalmente independente: cada diretório é um par, completo e logicamente independente dos outros diretórios que gere; não existe nenhuma relação principal-subordinado entre diretórios. Esta independência entre diretórios inclui independência de recursos, independência administrativa e independência de sincronização.

* **Independência de recursos**. Se criar ou eliminar um recurso num diretório, isso não terá impacto em nenhum outro recurso de nenhum outro diretório, com a exceção parcial dos utilizadores externos, descritos abaixo. Se utilizar um domínio personalizado “contoso.com” com um diretório, não o poderá utilizar com qualquer outro diretório.
* **Independência administrativa**.  Se um utilizador não administrativo do diretório “Contoso” criar um diretório de “Teste”, então:
  
  * A ferramenta de sincronização de diretórios, para sincronizar dados com uma floresta única do AD.
  * Os administradores do diretório “Contoso” não têm privilégios administrativos diretos para o diretório de “Teste”, exceto se um administrador de “Teste” lhes conceder esses privilégios. Os administradores da “Contoso” podem controlar o acesso ao diretório de “Teste” controlando a conta de utilizador que tiver criado o “Teste”.
    
    E, se alterar (adicionar ou remover) uma função de administrador de um utilizador num diretório, a alteração não afetará nenhuma função de administrador que o utilizador possa ter em qualquer outro diretório.
* **Independência de sincronização**. Pode configurar cada Azure AD independentemente para a sincronização dos dados a partir de uma única instância:
  
  * Da ferramenta de sincronização de diretórios, para sincronizar dados com uma floresta única do AD ou
  * Do Conector Azure Active Directory para Forefront Identity Manager, para sincronizar dados com uma ou mais florestas no local e/ou fontes de dados não AD.

Tenha também em atenção que, ao contrário de outros recursos do Azure, os diretórios não são recursos subordinados de uma subscrição do Azure. Por isso, se cancelar ou deixar que a subscrição do Azure expire, pode continuar a aceder aos dados do diretório através do Azure AD PowerShell, da Graph API do Azure ou de outras interfaces, como o Centro de Administração do Office 365. Também pode associar outra subscrição ao diretório.

## <a name="how-can-i-delete-an-azure-ad-directory"></a>Como posso eliminar um diretório do Azure AD?
Um administrador global pode eliminar um diretório do Azure AD a partir do portal. Quando um diretório é eliminado, também são eliminados todos os recursos contidos no diretório; por isso, certifique-se de que não precisa do diretório antes de o eliminar.

> [!NOTE]
> Se o utilizador tiver iniciado sessão com uma conta profissional ou escolar, o utilizador não deverá tentar eliminar o diretório raiz. Por exemplo, se o utilizador tiver iniciado sessão como joe@contoso.onmicrosoft.com,, esse utilizador não poderá eliminar o diretório contoso.onmicrosoft.com, uma vez que se trata do domínio predefinido.
> 
> 

### <a name="conditions-that-must-be-met-to-delete-an-azure-ad-directory"></a>Condições que devem cumprir para eliminar um diretório do Azure AD
O Azure AD requer que certas condições sejam cumpridas para eliminar um diretório. Isso reduz o risco de eliminar um diretório que poderia ter um impacto negativo sobre os utilizadores ou aplicações, como a capacidade de os utilizadores iniciarem sessão no Office 365 ou acederem a recursos no Azure. Por exemplo, se um diretório de uma subscrição tiver sido eliminado involuntariamente, os utilizadores não poderão aceder aos recursos do Azure daquela subscrição.

Verificam-se as seguintes condições:

* O único utilizador no diretório é o administrador global que irá eliminar o diretório. Deverão ser eliminados todos os outros utilizadores antes de eliminar o diretório. Se os utilizadores forem sincronizados no local, a sincronização terá de ser desativada e os utilizadores eliminados do diretório em nuvem através do Portal de Gestão ou do módulo do Azure para Windows PowerShell. Não é necessário eliminar grupos ou contactos, tais como contactos adicionados a partir do Centro de Administração do Office 365.
* Não podem existir aplicações no diretório. As aplicações devem ser eliminadas antes de eliminar o diretório.
* Não podem existir subscrições para quaisquer Serviços Online da Microsoft, como o Microsoft Azure, o Office 365 ou o Azure AD Premium, associadas ao diretório. Por exemplo, se tiver criado um diretório predefinido no Azure, não o poderá eliminar se a sua subscrição do Azure ainda depender desse diretório para a autenticação. Do mesmo modo, não pode eliminar um diretório se outro utilizador tiver uma subscrição associada. Para associar a subscrição a um diretório diferente, inicie sessão no Portal de Gestão do Azure e clique em **Definições** no painel de navegação esquerdo. Em seguida, na parte inferior da página **Subscrições**, clique em **Editar Diretório**. Para obter mais informações sobre as subscrições do Azure, consulte [Como estão associadas as subscrições do Azure ao Azure AD](active-directory-how-subscriptions-associated-directory.md).

> [!NOTE]
> Se o utilizador tiver iniciado sessão com uma conta profissional ou escolar, o utilizador não deverá tentar eliminar o diretório raiz. Por exemplo, se o utilizador tiver iniciado sessão como joe@contoso.onmicrosoft.com,, esse utilizador não poderá eliminar o diretório contoso.onmicrosoft.com, uma vez que se trata do domínio predefinido.
> 
> 

* Não podem ser associados ao diretório nenhuns fornecedores Multi-Factor Authentication.

## <a name="additional-resources"></a>Recursos Adicionais
* [Fórum do Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
* [Fórum do Multi-Factor Authentication do Azure](https://social.msdn.microsoft.com/Forums/home?forum=windowsazureactiveauthentication)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/azure)
* [Inscrever-se no Azure como uma organização](sign-up-organization.md)
* [Gerir o Azure AD com o Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
* [Atribuir funções de administrador no Azure AD](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-administer/aad_portals.png
[2]: ./media/active-directory-administer/azure_tenants.png



<!--HONumber=Dec16_HO1-->


