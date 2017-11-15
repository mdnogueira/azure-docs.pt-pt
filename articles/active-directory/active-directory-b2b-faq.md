---
title: "Colaboração B2B do Active Directory Azure perguntas mais frequentes | Microsoft Docs"
description: "Obtenha respostas às perguntas mais frequentes sobre a colaboração B2B do Azure Active Directory do."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: 0e3ae7f176f41b4b0984dbef0748451c7ebff9e7
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Colaboração B2B do Active Directory Azure perguntas mais frequentes

Estas perguntas mais frequentes (FAQ) sobre a colaboração do Azure Active Directory (Azure AD) empresa-empresa (B2B) periodicamente são atualizadas para incluir novos tópicos.

### <a name="is-azure-ad-b2b-collaboration-available-in-the-azure-classic-portal"></a>Colaboração B2B do Azure AD está disponível no portal clássico do Azure?
Não. Funcionalidades de colaboração do Azure AD B2B só estão disponíveis no [portal do Azure](https://portal.azure.com) e no [painel de acesso](https://myapps.microsoft.com/). 

### <a name="can-we-customize-our-sign-in-page-so-it-is-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Iremos pode personalizar a nossa página de início de sessão para que seja mais intuitiva dos nossos utilizadores de convidados de colaboração B2B?
Absolutamente! Consulte a nossa [blogue sobre esta funcionalidade](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Para obter mais informações sobre como personalizar a página de início de sessão da sua organização, consulte [adicionar imagem corporativa para iniciar sessão e painel de acesso](customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Os utilizadores de colaboração do B2B podem aceder SharePoint Online e OneDrive?
Sim. No entanto, é a capacidade de pesquisar utilizadores convidados existentes no SharePoint Online utilizando o selecionador de pessoas **desativar** por predefinição. Para ativar a opção para procurar utilizadores convidados existentes, defina **ShowPeoplePickerSuggestionsForGuestUsers** para **no**. Pode ativar esta definição ao nível do inquilino ou ao nível da coleção de sites. Pode alterar esta definição, utilizando os cmdlets Set-SPOTenant e SPOSite de conjunto. Com estes cmdlets, membros podem procurar todos os utilizadores convidados existentes no diretório. As alterações no âmbito de inquilino não afetam os sites do SharePoint Online que já foram aprovisionados.

### <a name="is-the-csv-upload-feature-still-supported"></a>A funcionalidade de carregamento CSV ainda é suportada?
Sim. Para obter mais informações sobre como utilizar a funcionalidade de carregamento do ficheiro. csv, consulte [este exemplo do PowerShell](active-directory-b2b-code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Como personalizar o meu mensagens de e-mail de convite?
Pode personalizar quase tudo sobre o processo de inviter utilizando o [B2B convite APIs](active-directory-b2b-api.md).

### <a name="can-an-invited-external-user-leave-the-organization-after-being-invited"></a>Um utilizador externo convidado pode deixar a organização após a ser convidou?
O administrador da organização convidando pode eliminar um utilizador de convidado de colaboração B2B do respetivo diretório, mas o utilizador convidado não pode deixar o diretório da organização convidando por si mesmos. 

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Os utilizadores convidados podem repor o respetivo método de autenticação multifator?
Sim. Os utilizadores convidados podem repor o método de autenticação multifator da mesma forma que os utilizadores regulares fazem.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Que organização é responsável por licenças de autenticação multifator?
A organização convidando efetua a autenticação multifator. A organização convidando tem de se certificar de que a organização tiver licenças suficientes para os respetivos utilizadores B2B que estão a utilizar a autenticação multifator.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>E se uma organização de parceiro já tem de configurar a autenticação multifator? Estamos a autenticação multifator de confiança e utiliza a nossa própria autenticação multifator?
Esta funcionalidade está a ser planeada para uma versão futura, pelo que, em seguida, que pode selecionar parceiros específicos a excluir do seu a autenticação multifator (convidando da organização).

### <a name="how-can-i-use-delayed-invitations"></a>Como utilizar o convites atrasadas?
Uma organização poderá querer adicionar utilizadores de colaboração do B2B, aprovisioná-los para aplicações, conforme necessário e, em seguida, enviar convites para. Pode utilizar o convite de colaboração B2B API para personalizar o fluxo de trabalho de integração.

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Eu fizer um utilizador convidado um administrador limitado?
Com certeza. Para obter mais informações, consulte [adicionar utilizadores de convidado para uma função](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Colaboração B2B do Azure AD permite que os utilizadores de B2B aceder ao portal do Azure?
A menos que um utilizador é atribuído a função de administrador global ou limitado, os utilizadores de colaboração do B2B não necessitam de acesso ao portal do Azure. No entanto, os utilizadores de colaboração do B2B que estão atribuídos a função de administrador global ou limitado podem aceder ao portal. Além disso, se um utilizador de convidados que não está atribuído uma dessas funções de administrador aceder ao portal, o utilizador poderá conseguir aceder a determinados partes da experiência. A função de utilizador convidado tem algumas permissões no diretório.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Pode bloquear acesso ao portal do Azure para os utilizadores convidados?
Sim! Quando configurar esta política, tenha cuidado para evitar acidentalmente bloqueio acesso aos membros e administradores.
Para bloquear o acesso de um utilizador convidado para o [portal do Azure](https://portal.azure.com), utilize uma política de acesso condicional a API de modelo de implementação clássica do Windows Azure:
1. Modificar o **todos os utilizadores** para que contém apenas os membros de grupo.
  ![modificar a captura de ecrã de grupo](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Crie um grupo dinâmico que contenha os utilizadores convidados.
  ![Criar grupo captura de ecrã](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Configure uma política de acesso condicional para impedir que os utilizadores de convidado de aceder ao portal, conforme mostrado no vídeo seguinte:
  
  > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Colaboração B2B do Azure AD suporta autenticação multifator e contas de correio eletrónico de consumidor?
Sim. Contas de correio eletrónico de multi-factor authentication e de consumidor são suportadas para colaboração B2B do Azure AD.

### <a name="do-you-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Planear suportar a palavra-passe, repor para os utilizadores de colaboração B2B do Azure AD?
Sim. Seguem-se os detalhes importantes para a reposição de palavra-passe self-service (SSPR) para um utilizador de B2B que está convidado a partir de uma organização de parceiro:
 
* SSPR ocorre apenas no inquilino identidade do utilizador B2B.
* Se o inquilino de identidade é uma conta Microsoft, é utilizada a mecanismo SSPR de conta Microsoft.
* Se o inquilino de identidade é uma just-in-time (JIT) ou de inquilino "viral", é enviada uma mensagem de e-mail de reposição de palavra-passe.
* Para os outros inquilinos, o processo SSPR padrão é seguido para utilizadores B2B. Como membro SSPR para os utilizadores de B2B, no contexto do recurso, os inquilinos está bloqueado. 

### <a name="is-password-reset-available-for-guest-users-in-a-just-in-time-jit-or-viral-tenant-who-accepted-invitations-with-a-work-or-school-email-address-but-who-didnt-have-a-pre-existing-azure-ad-account"></a>É a palavra-passe reposto utilizadores convidados um just-in-time (JIT) ou "viral" inquilino que aceitaram convites com um trabalho ou endereço de e-mail profissional, mas que não tenham uma conta do Azure AD pré-existente?
Sim. Pode ser enviado um e-mail de reposição de palavra-passe que permite ao utilizador para repor a palavra-passe de inquilinos JIT.

### <a name="does-microsoft-dynamics-crm-provide-online-support-for-azure-ad-b2b-collaboration"></a>Microsoft Dynamics CRM fornecem suporte online para colaboração B2B do Azure AD?
Atualmente, a Microsoft Dynamics CRM não fornece suporte online para colaboração B2B do Azure AD. No entanto, planeamos suportar isto no futuro.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>O que é a duração de uma palavra-passe inicial para um utilizador recentemente criado de colaboração B2B?
Azure AD tem um conjunto fixo de caráter, força de palavra-passe e conta de contas de utilizador de nuvem de requisitos de bloqueio que aplicam-se igualmente a todas as do Azure AD. Contas de utilizador de nuvem são contas que não federadas com o outro fornecedor de identidade, tais como 
* Conta Microsoft
* Facebook
* Serviços de Federação do Active Directory
* Outro inquilino de nuvem (para colaboração B2B)

Para contas federadas, política de palavra-passe depende a política que é aplicada o inquilinos no local e definições da conta Microsoft do utilizador.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Uma organização poderá querer ter experiências diferentes nas respetivas aplicações para utilizadores de inquilino e os utilizadores convidados. Existe orientações padrão para isto? É o modelo correto para utilizar a afirmação a presença do fornecedor de identidade?
 Um utilizador convidado pode utilizar qualquer fornecedor de identidade para autenticar. Para obter mais informações, consulte [propriedades de um utilizador de colaboração B2B](active-directory-b2b-user-properties.md). Utilize o **UserType** propriedade para determinar a experiência de utilizador. O **UserType** afirmação não está atualmente incluída no token. As aplicações devem utilizar a Graph API para consultar o diretório para o utilizador bem como obter o UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Onde posso encontrar uma Comunidade de colaboração B2B para partilhar as soluções e enviar ideias?
Estamos constantemente está a escutar para os seus comentários para melhorar a colaboração B2B. Convidamo-lo para partilhar o seu utilizador cenários, as melhores práticas e o que gostou do colaboração B2B do Azure AD. Associar o debate [Microsoft técnico Comunidade](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Também Convidamo-lo para submeter as suas ideias e votar para futuras funcionalidades em [ideias de colaboração do B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Podemos enviar um convite que é automaticamente resgatado, para que o utilizador é apenas "pronto para ir"? Ou a do utilizador sempre tem de clicar para o URL de resgate?
Convites para que são enviadas por um utilizador na organização convidando que também seja membro da organização do parceiro não necessitam de resgate pelo utilizador B2B.

Recomendamos que convidar um utilizador da organização de parceiro para associar a organização convidando. [Adicione este utilizador à função de inviter do convidado na organização de recursos](active-directory-b2b-add-guest-to-role.md). Este utilizador pode convidar outros utilizadores na organização do parceiro com a início de sessão IU, scripts do PowerShell ou APIs. Em seguida, os utilizadores de colaboração do B2B da organização não são necessários para resgatar as respetivas convites.

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Como funciona colaboração B2B quando o parceiro convidado através de Federação para adicionar as seus próprios autenticação no local?
Se o parceiro tem um inquilino do Azure AD é federado para a infraestrutura de autenticação no local, no local-início de sessão único (SSO) é automaticamente alcançado. Se o parceiro não tiver um inquilino do Azure AD, é criada uma conta do Azure AD para os novos utilizadores. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Posso considerar o Azure AD B2B não aceitou gmail.com e outlook.com endereços de correio eletrónico e que B2C foi utilizado para esses tipos de contas?
Estamos a remover as diferenças entre B2B e colaboração (B2C) de empresa-empresa relativamente a quais identidades são suportadas. A identidade utilizada não é um bom motivo para escolher entre utilizar B2B ou utilizar B2C. Para obter informações sobre como escolher a opção de colaboração, consulte [colaboração B2B comparar e B2C no Azure Active Directory](active-directory-b2b-compare-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>As aplicações e serviços suportam os utilizadores convidados de B2B do Azure?
Todas as aplicações do Azure integrada no AD suportam os utilizadores convidados de B2B do Azure. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Iremos forçar multi-factor authentication para os utilizadores convidados de B2B se nossos parceiros de não tem a autenticação multifator?
Sim. Para obter mais informações, consulte [acesso condicional para os utilizadores de colaboração do B2B](active-directory-b2b-mfa-instructions.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>No SharePoint, pode definir uma lista de "" ou "negadas" para os utilizadores externos. Vamos fazer isto no Azure?
Sim. Suporta a colaboração do Azure AD B2B apresenta uma lista de permissão e negação listas. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>As licenças é necessário utilizar o Azure AD B2B?
Para obter informações sobre o que licenças as necessidades da organização para utilizar o Azure AD B2B, consulte [colaboração do Azure Active Directory B2B licenciamento orientações](active-directory-b2b-licensing.md).

### <a name="next-steps"></a>Passos seguintes

Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como é que os administradores do Azure AD adicionar utilizadores de colaboração do B2B?](active-directory-b2b-admin-add-users.md)
* [Como é que os infotrabalhadores adicionar utilizadores de colaboração do B2B?](active-directory-b2b-iw-add-users.md)
* [Os elementos do e-mail de convite de colaboração B2B](active-directory-b2b-invitation-email.md)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento e colaboração do Azure AD B2B](active-directory-b2b-licensing.md)
* [Resolução de problemas de colaboração B2B do Azure AD](active-directory-b2b-troubleshooting.md)
* [Azure AD B2B colaboração API e personalização](active-directory-b2b-api.md)
* [Autenticação multifator para os utilizadores da colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Adicionar utilizadores de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de artigos da gestão de aplicações no Azure AD](active-directory-apps-index.md)
