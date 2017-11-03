---
title: "Como requerer a autenticação multifator | Microsoft Docs"
description: "Saiba como requerer a autenticação multifator (MFA) para as identidades privilegiadas com a extensão do Azure Active Directory Privileged Identity Management."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 1e3dc4ad-3a6a-4a52-8417-3ca4f84ae05c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b778774fa23be8219db3f716d79bac324a7de9d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Como requerer a MFA no Azure AD Privileged Identity Management
Recomendamos que necessitam de autenticação multifator (MFA) para todos os seus administradores. Isto reduz o risco de um ataque devido a uma palavra-passe comprometido.

Pode exigir que os utilizadores ser um desafio MFA quando iniciam sessão no. A mensagem de blogue [MFA para Office 365 e o MFA do Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) compara o que está incluído em subscrições do Office e do Azure, com as funcionalidades incluídas na oferta Microsoft Azure multi-factor Authentication.

Também pode exigir que os utilizadores ser um desafio MFA quando ativar a uma função no Azure AD PIM. Desta forma, se o utilizador não concluiu um desafio MFA quando que tem sessão iniciada, será pedido para o fazer por PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Exigir a MFA no Azure AD Privileged Identity Management
Quando gerir identidades no PIM como um administrador com função privilegiada, poderá ver alertas que recomendamos MFA para contas com privilégios. Clique no alerta de segurança no dashboard do PIM e um novo painel será aberto com uma lista das contas de administrador que deve exigir a MFA.  Pode exigir a MFA, selecionando a várias funções e, em seguida, clicando a **corrigir** botão, ou clique nas reticências junto funções individuais e, em seguida, clique em de **corrigir** botão.

> [!IMPORTANT]
> Com o botão direito agora, o MFA do Azure só funciona com o trabalho contas escolares ou profissionais, não as contas Microsoft (normalmente, uma conta pessoal que é utilizado para iniciar sessão nos serviços Microsoft Skype, Xbox, Outlook.com, etc.). Por este motivo, qualquer pessoa com uma conta Microsoft não pode ser um administrador elegível porque estes não podem utilizar a MFA para ativar as respetivas funções. Se precisam destes utilizadores continuar a gerir as cargas de trabalho através de uma conta Microsoft, efetuar a elevação-las para os administradores permanentes por agora.
> 
> 

Além disso, pode alterar o requisito de MFA para uma função específica ao clicar no mesmo na secção de funções do dashboard do PIM. Em seguida, clique em **definições** no painel de função e, em seguida, selecionar **ativar** em autenticação multifator.

## <a name="how-azure-ad-pim-validates-mfa"></a>Como o Azure AD PIM valida a MFA
Existem duas opções para validar a MFA quando um utilizador ativa uma função.

A opção mais simples é a depender do MFA do Azure para utilizadores que esteja a ativar uma função com privilégios. Para tal, verifique primeiro que esses utilizadores são licenciados, se necessário e tem registado para MFA do Azure. Obter mais informações sobre como efetuar esta ação estão a ser [introdução ao Azure multi-factor Authentication na nuvem](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md). É recomendado, mas não ser obrigatório, que pode configura o Azure AD para impor a MFA para estes utilizadores quando iniciam sessão no. Isto acontece porque as verificações MFA serão efetuadas pelo Azure AD PIM em si.

Em alternativa, se os utilizadores autenticam no local pode ter o seu fornecedor de identidade serão responsáveis pela MFA. Por exemplo, se tiver configurado os serviços de Federação do AD para exigir a autenticação baseada em smart card antes de aceder ao Azure AD, [proteger recursos da nuvem com o Azure multi-factor Authentication e o AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) inclui instruções para configurar o AD FS para enviar afirmações com o Azure AD. Quando um utilizador tenta ativar uma função, o Azure AD PIM aceitará que MFA já foi validada para o utilizador assim que recebe as afirmações adequadas.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

