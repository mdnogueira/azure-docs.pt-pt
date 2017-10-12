---
title: FAQ do Azure Active Directory | Microsoft Docs
description: "As FAQ do Azure Active Directory respondem a perguntas sobre como aceder ao Azure e ao Azure Active Directory, gestão de palavras-passe e acesso à aplicação."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/16/2017
ms.author: markvi
ms.openlocfilehash: 8d4460b3059558de2253c6f6a2d2fc8e7564d6d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-faq"></a>FAQ do Azure Active Directory
O Azure Active Directory (Azure AD) é uma solução identidade como um Serviço (IDaaS) completa que abrange todos os aspetos relativos à identidade, gestão de acesso e segurança.

Para obter mais informações, consulte [What is Azure Active Directory? (O que é o Azure Active Directory?)](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Aceder ao Azure e Azure Active Directory
**P: Por que motivo recebo “Não foram encontradas subscrições” ao tentar aceder ao Azure AD no portal clássico do Azure?**

**R:** Para aceder ao portal clássico do Azure, cada utilizador precisa de permissões com uma subscrição do Azure. Se tiver uma subscrição paga do Office 365 ou do Azure AD, aceda a [http://aka.ms/accessAAD](http://aka.ms/accessAAD) com apenas um passo único de ativação. Caso contrário, terá de ativar uma [conta do Azure](https://azure.microsoft.com/pricing/free-trial/) gratuita ou uma subscrição paga.

Para obter mais informações, consulte:

* [Como as subscrições do Azure estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gerir o diretório da subscrição do Office 365 no Azure](active-directory-manage-o365-subscription.md)

- - -
**P: Qual é a relação entre o Azure AD, o Office 365 e o Azure?**

**R:** O Azure AD fornece-lhe capacidades de identidade e de acesso comuns a todos os serviços da Web. Se estiver a utilizar o Office 365, o Microsoft Azure, o Intune ou outros, já está a utilizar o Azure AD para ajudar a ativar o início de sessão e gestão de acesso a todos estes serviços.

Todos os utilizadores que estão configurados para utilizar serviços da web estão definidos como contas de utilizador numa ou mais instâncias do Azure AD. Pode configurar estas contas para obter gratuitamente capacidades do Azure AD, como o acesso a aplicações na cloud.

Os serviços pagos do Azure AD, como o Enterprise Mobility + Security complementam outros serviços da Web, como o Office 365 e o Microsoft Azure com soluções abrangentes de gestão empresarial e de segurança.
- - -
**P: Porque é que posso iniciar sessão no portal do Azure, mas não no portal clássico do Azure?**

**R:** O portal do Azure não necessita de uma subscrição válida e o portal clássico necessita de uma subscrição válida.  Se não tiver uma subscrição, não poderá iniciar sessão no portal clássico.
- - -
**P: Quais as diferenças entre Administrador da Subscrição e Administrador do Diretório?**

**R:** Por predefinição, quando se inscreve no Azure, é-lhe atribuída a função de Administrador da Subscrição. O administrador da subscrição pode utilizar uma conta Microsoft ou uma conta profissional ou escolar do diretório ao qual a subscrição do Azure está associada.  Esta função está autorizada a gerir serviços no portal do Azure.

Se outras pessoas tiverem de iniciar sessão e aceder aos serviços com a mesma subscrição, pode adicioná-las como coadministradores. Esta função tem os mesmos privilégios de acesso do administrador de serviços, mas não pode alterar a associação de subscrições a diretórios do Azure.  Para obter mais informações sobre os administradores da subscrição, consulte [How to add or change Azure administrator roles (Como adicionar ou alterar funções de administrador do Azure)](../billing-add-change-azure-subscription-administrator.md) e [How Azure subscriptions are associated with Azure Active Directory (Como as subscrições do Azure estão associadas ao Azure Active Directory)](active-directory-how-subscriptions-associated-directory.md).


O Azure AD tem um conjunto diferente de funções administrativas para gerir o diretório e as funções relacionadas com a identidade.  Estes administradores terão acesso às várias funcionalidades no portal do Azure ou no portal clássico do Azure. A função do administrador determina o que pode fazer, como criar ou editar utilizadores, atribuir funções administrativas a outras pessoas, repor palavras-passe de utilizador, gerir licenças de utilizador ou gerir domínios.  Para obter mais informações sobre os administradores de diretório do Azure AD e as respetivas funções, consulte [Assigning administrator roles in Azure Active Directory (Atribuir funções de administrador no Azure Active Directory)](active-directory-assign-admin-roles.md).

Além disso, os serviços pagos do Azure AD, como o Enterprise Mobility + Security complementam outros serviços da Web, como o Office 365 e o Microsoft Azure com soluções abrangentes de gestão empresarial e de segurança.

- - -
**P: Existe um relatório que mostra quando as minhas licenças de utilizador do Azure AD expiram?**

**R:** Não.  Esta configuração não está disponível no momento.

- - -

## <a name="get-started-with-hybrid-azure-ad"></a>Introdução ao Azure AD Híbrido


**P: Como deixo um inquilino quando estou adicionado como um colaborador?**

**R:** Quando é adicionado ao inquilino de outra organização como um colaborador, pode utilizar o "comutador do inquilino" na parte superior direita para alternar entre inquilinos.  Atualmente, não existe nenhuma forma de deixar a organização que o convidou. A Microsoft está a trabalhar para dispor desta funcionalidade.  Até que esta funcionalidade esteja disponível, pode pedir à organização que o convidou para o remover do seu inquilino.
- - -
**P: Como ligar o meu diretório no local ao Azure AD?**

**R:** Pode ligar o diretório no local ao Azure AD com o Azure AD Connect.

Para obter mais informações, consulte [Integrating your on-premises identities with Azure Active Directory (Integrar as identidades no local ao Azure Active Directory)](active-directory-aadconnect.md).

- - -
**P: Como posso configurar o SSO entre o meu diretório no local e as minhas aplicações na nuvem?**

**R:** Só tem de configurar o início de sessão único (SSO) entre o seu diretório no local e o Azure AD. Enquanto aceder às suas aplicações na nuvem através do Azure AD, o serviço orienta automaticamente os utilizadores para uma autenticação correta com as suas credenciais no local.

A implementação do SSO no local poderá ser feita facilmente com soluções de federação, tais como o Active Directory Federation Services (AD FS) ou ao configurar a sincronização hash de palavras-passe. Pode facilmente implementar ambas as opções utilizando o assistente de configuração do Azure AD Connect.

Para obter mais informações, consulte [Integrating your on-premises identities with Azure Active Directory (Integrar as identidades no local ao Azure Active Directory)](active-directory-aadconnect.md).

- - -
**P: O Azure AD fornece um portal personalizado aos utilizadores da minha organização?**

**R:** Sim, o Azure AD fornece o acesso ao [Painel de Acesso do Azure AD](http://myapps.microsoft.com) para acesso a utilizadores pessoalizados e a aplicações. Se for um cliente do Office 365, poderá encontrar muitas das mesmas funcionalidades no portal do Office 365.

Para mais informações, consulte [Introduction to the Access Panel (Introdução ao Painel de Acesso)](active-directory-saas-access-panel-introduction.md).

- - -
**P: O Azure AD ajuda-me a gerir a minha infraestrutura no local?**

**R:** Sim. A edição Premium do Azure AD fornece o Azure AD Connect Health. O Azure AD Connect Health ajuda a monitorizar e obter informações sobre a sua infraestrutura de identidade no local, bem como sobre os serviços de sincronização.  

Para obter mais informações, consulte [Monitor your on-premises identity infrastructure and synchronization services in the cloud (Monitorizar os serviços de infraestrutura de identidade no local e sincronização na cloud)](active-directory-aadconnect-health.md).  

- - -
## <a name="password-management"></a>Gestão de palavras-passe
**P: Posso utilizar a repetição de escrita de palavras-passe do Azure AD sem sincronizar a palavra-passe? (Neste cenário, é possível utilizar a reposição de palavra-passe self-service do Azure AD (SSPR) com a repetição de escrita de palavras-passe e não armazenar palavras-passe na cloud?)**

**R:** Não tem de sincronizar as palavras-passe do Active Directory no Azure AD para ativar a repetição de escrita de palavras-passe. Num ambiente federado, o início de sessão único (SSO) do Azure AD depende do diretório no local para autenticar o utilizador. Este cenário não requer que a palavra-passe no local seja controlada no Azure AD.

- - -
**P: Quanto tempo demora a repetição de escrita de uma palavra-passe no Active Directory no local?**

**R:** a repetição de palavras-passe é feita em tempo real.

Para mais informações, consulte [Introdução à gestão de palavras-passe](active-directory-passwords-getting-started.md)

- - -
**P: Posso utilizar a repetição de palavras-passe com palavras-passe geridas por um administrador?**

**R:** Sim, se tiver ativado a repetição de palavras-passe, as operações de palavras-passe feitas por um administrador são repetidas no ambiente local.  

Para obter mais respostas a perguntas relacionadas com palavras-passe, consulte [Password management frequently asked questions (Perguntas mais frequentes da gestão de palavras-passe)](active-directory-passwords-faq.md).
- - -
**P: O que posso fazer se não me lembrar da palavra-passe do Office 365/Azure AD quando tentar alterar a minha palavra-passe?**

**R:** Para este tipo de situação, existem algumas opções.  Utilize a reposição de palavra-self-service (SSPR) se estiver disponível.  A SSPR funciona consoante a forma como está configurada.  Para mais informações, veja [How does the password reset portal work (Como funciona o portal de reposição de palavras-passe)](active-directory-passwords-best-practices.md).

Para os utilizadores do Office 365, o administrador pode repor a palavra-passe através dos passos descritos em [Reset user passwords (Repor palavras-passe de utilizador)](https://support.office.com/en-us/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

Para contas do Azure AD, os administradores podem repor as palavras-passe através de um dos seguintes procedimentos:

- [Repor contas no portal do Azure](active-directory-users-reset-password-azure-portal.md)
- [Repor contas no portal clássico](active-directory-create-users-reset-password.md)
- [Utilizar o PowerShell](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


- - -
## <a name="security"></a>Segurança
**P: As contas são bloqueadas ao fim de um determinado número de tentativas falhadas ou é utilizada outra estratégia mais sofisticada?**</br>
Utilizamos uma estratégia mais sofisticada para bloquear as contas.  Baseia-se no IP do pedido e nas palavras-passe introduzidas. A duração do bloqueio também aumenta com base na probabilidade de se tratar de um ataque.  

**P: Algumas palavras-passe (comuns) são rejeitadas com a mensagem “esta palavra-passe foi utilizada demasiadas vezes”. Isto refere-se a palavras-passe utilizadas no diretório ativo atual?**</br>
Refere-se a palavras-passe que são globalmente comuns, como as variantes de “Palavra-passe” e “123456”.

**P: Os pedidos de início de sessão de origens duvidosas (botnets, pontos finais de tor) vão ser bloqueados em inquilinos B2C ou é necessário para tal um inquilino da edição Básica ou Premium?**</br>
Temos um gateway que filtra os pedidos e que oferece alguma proteção contra botnets, que é aplicado a todos os inquilinos B2C.

## <a name="application-access"></a>Acesso à aplicação
**P: Onde posso encontrar uma lista das aplicações previamente integradas ao Azure AD e as respetivas funcionalidades?**

**R:** O Azure AD tem mais de 2.600 aplicações previamente integradas da Microsoft, de fornecedores de serviços de aplicações e parceiros. Todas as aplicações previamente integradas suportam o início de sessão único (SSO). O SSO permite-lhe utilizar as credenciais organizacionais para aceder às suas aplicações. Algumas das aplicações também suportam o aprovisionamento e cancelamento de aprovisionamento automatizados.

Para obter a lista completa das aplicações previamente integradas, consulte [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

- - -
**P: E se a aplicação de que preciso não fizer parte do Azure AD Marketplace?**

**R:** Com o Azure AD Premium, pode adicionar e configurar qualquer aplicação que desejar. Consoante as funcionalidades da aplicação e das suas preferências, pode configurar o SSO e o aprovisionamento automatizado.  

Para obter mais informações, consulte:

* [Configurar o início de sessão único em aplicações que não fazem parte da galeria de aplicações do Azure Active Directory](active-directory-saas-custom-apps.md)
* [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](active-directory-scim-provisioning.md)

- - -
**P: Como os utilizadores iniciam sessão em aplicações com o Azure AD?**

**R:** O Azure AD oferece aos utilizadores várias formas de visualizarem e acederem às respetivas aplicações, tais como:

* O painel de acesso do Azure AD
* O iniciador de aplicações do Office 365
* Início de sessão direto em aplicações federadas
* Ligações avançadas para aplicações federadas, baseadas em palavras-passe ou existentes

Para mais informações, consulte [Implementar aplicações integradas ao Azure AD para os utilizadores](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

- - -
**P: Quais são as várias formas de autenticação e início de sessão único do Azure AD das aplicações?**

**R:** O Azure AD suporta vários protocolos normalizados para a autenticação e autorização, tais como SAML 2.0, OpenID Connect, OAuth 2.0 e WS-Federation. O Azure AD também suporta cofres de palavras-passe e as capacidades de início de sessão automatizado para aplicações que apenas suportam autenticação baseada em formulários.  

Para obter mais informações, consulte:

* [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md)
* [Protocolos de autenticação do Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Como funciona um início de sessão único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

- - -
**P: Posso adicionar as aplicações que estiver a executar no local?**

**R:** O Proxy da Aplicação do Azure AD fornece um acesso fácil e seguro às Web Apps no local que escolher. Pode aceder a estas aplicações da mesma forma a que acede às aplicações de software como um serviço (SaaS) no Azure AD. Não é necessário nenhuma VPN ou alterar a infraestrutura de rede.  

Para mais informações, consulte [How to provide secure remote access to on-premises applications (Como fornecer acesso remoto seguro a aplicações no local)](active-directory-application-proxy-get-started.md).

- - -
**P: Como posso exigir a autenticação multifator para utilizadores que acedam a uma determinada aplicação?**

**R:** Com o acesso condicional do Azure AD, pode atribuir uma política de acesso único a cada aplicação. Na sua política, pode exigir a autenticação multifator sempre ou quando os utilizadores não estiverem ligados à rede local.  

Para mais informações, consulte [Securing access to Office 365 and other apps connected to Azure Active Directory (Proteger o acesso ao Office 365 e a outras aplicações ligadas ao Azure Active Directory)](active-directory-conditional-access.md).

- - -
**P: O que é o aprovisionamento automatizado do utilizador para aplicações SaaS?**

**R:** Utilize o Azure AD para automatizar a criação, a manutenção e a remoção de identidades de utilizadores em diversas aplicações SaaS na cloud populares.

Para obter mais informações, consulte [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory (Automatizar o aprovisionamento do utilizador e cancelar o aprovisionamento a aplicações SaaS com o Azure Active Directory)](active-directory-saas-app-provisioning.md).

- - -
**P: Posso configurar uma ligação LDAP segura ao Azure AD?** 

**R:** Não. O Azure AD não suporta o protocolo LDAP.
