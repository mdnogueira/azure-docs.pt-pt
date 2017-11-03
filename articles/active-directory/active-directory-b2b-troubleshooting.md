---
title: "Resolução de problemas de colaboração B2B do Azure Active Directory do | Microsoft Docs"
description: "Responsabilidade para problemas comuns com a colaboração B2B do Azure Active Directory do"
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
ms.date: 05/25/2017
ms.author: sasubram
ms.openlocfilehash: 2009cfc956a2703e268c9364996aa2d0fbd8f279
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Resolução de problemas de colaboração B2B do Azure Active Directory do

Seguem-se algumas responsabilidade para problemas comuns com a colaboração B2B do Azure Active Directory (Azure AD).


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Posso adicionar um utilizador externo, mas não vê-los no meu livro de endereços Global ou no selecionador de pessoas

Em casos onde os utilizadores externos não são preenchidos na lista, o objeto poderá demorar alguns minutos a replicar.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Um utilizador de convidado B2B não está visível no seleccionador do SharePoint Online/OneDrive pessoas 
 
A capacidade de pesquisar utilizadores convidados existentes no selecionador de pessoas SharePoint Online (SPO) está DESATIVADA por predefinição para corresponder ao comportamento de legado.

Pode ativar esta funcionalidade, utilize a definição 'ShowPeoplePickerSuggestionsForGuestUsers' ao nível da coleção inquilino e o site. Pode definir a funcionalidade através dos cmdlets Set-SPOTenant e Set-SPOSite, que permite que os membros procurar todos os utilizadores convidados existentes no diretório. As alterações no âmbito de inquilino não afetam os sites SPO já aprovisionadas.

## <a name="invitations-have-been-disabled-for-directory"></a>Foram desativadas convites para o diretório

Se for notificado de que não têm permissões para convidar utilizadores, certifique-se de que a sua conta de utilizador está autorizada para convidar utilizadores externos em definições de utilizador:

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

Se modificar estas definições recentemente ou atribuída a função de convidado Inviter para um utilizador, poderá haver um atraso de 15-60 minutos antes das alterações surtam efeito.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>O utilizador que posso convidado está a receber um erro durante a resgate

Erros comuns incluem:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Administrador do invitee não permitiu a EmailVerified utilizadores de que está a ser criada no seu inquilino

Quando inviting utilizadores cuja organização está a utilizar o Azure Active Directory, mas em que a conta de utilizador específico não existe (por exemplo, o utilizador não existe no Azure AD contoso.com). O administrador de contoso.com pode ter uma política no local a impedir que os utilizadores a ser criada. O utilizador tem de verificar com o seu administrador para determinar se os utilizadores externos são permitidos. Administração do utilizador externo poderá ter de permitir aos utilizadores verificar E-Mail no respetivo domínio (consulte este [artigo](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) em permitir aos utilizadores verificar de E-Mail).

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Utilizador externo não existe já num domínio federado

Se estiver a utilizar a autenticação de Federação e o utilizador ainda não existir no Azure Active Directory, o utilizador não pode ser convidado.

Para resolver este problema, o administrador do utilizador externo tem de sincronizar a conta de utilizador para o Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Como dos does\#', que não é normalmente um caráter válido, a sincronização com o Azure AD?

"\#" é um caráter reservado UPNs para colaboração B2B do Azure AD ou utilizadores externos, porque a conta de convidados user@contoso.com fica user_contoso.com#EXT@fabrikam.onmicrosoft.com. Por conseguinte, \# UPNs feitos no local não são permitidos para iniciar sessão no portal do Azure. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Recebo um erro ao adicionar utilizadores externos a um grupo sincronizado

Os utilizadores externos podem ser adicionados apenas aos grupos de "Segurança" ou "atribuído" e não para grupos de controlado no local.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Os meus utilizadores externos não recebeu uma mensagem de e-mail para resgatar

O invitee deve verificar junto do respetivo filtro ISP ou spam para se certificar de que é permitido o seguinte endereço:Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Posso tenha em atenção que a mensagem personalizada não obter incluída com mensagens convite por vezes

Para estar em conformidade com as leis de privacidade, os APIs não incluírem mensagens personalizadas o convite de e-mail quando:

- O inviter não tem um endereço de e-mail no inquilino convidando
- Quando um principal de serviço aplicacional envia o convite

Se este cenário é importante para si, pode suprimir os nosso e-mail de convite de API e enviá-lo através do mecanismo de e-mail da sua preferência. Consulte a legais counsel sua organização para se certificar de qualquer e-mail enviar que desta forma também estas estejam em conformidade com as leis de privacidade.

## <a name="next-steps"></a>Passos seguintes

Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como é que os administradores de Azure Active Directory adicionar utilizadores de colaboração do B2B?](active-directory-b2b-admin-add-users.md)
* [Como é que os infotrabalhadores adicionar utilizadores de colaboração do B2B?](active-directory-b2b-iw-add-users.md)
* [Os elementos do e-mail de convite de colaboração B2B](active-directory-b2b-invitation-email.md)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento e colaboração do Azure AD B2B](active-directory-b2b-licensing.md)
* [Colaboração do Azure Active Directory B2B perguntas mais frequentes (FAQ)](active-directory-b2b-faq.md)
* [Colaboração B2B do Active Directory Azure API e personalização](active-directory-b2b-api.md)
* [Autenticação multifator para os utilizadores da colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Adicionar utilizadores de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
