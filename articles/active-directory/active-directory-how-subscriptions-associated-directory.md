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
ms.date: 08/24/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 081cf2bde44a0b55508cc7f0197fa7f8e378189b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-azure-subscriptions-are-associated-with-azure-active-directory"></a>Como as subscrições do Azure estão associadas ao Azure Active Directory
Este artigo inclui informações sobre a relação entre a subscrição do Azure e o Azure Active Directory (Azure AD) e como adicionar uma subscrição existente ao diretório do Azure AD.

## <a name="your-azure-subscriptions-relationship-to-azure-ad"></a>Relação da sua subscrição do Azure com o Azure AD
A sua subscrição do Azure tem uma relação de confiança com o Azure AD, o que significa que confia no diretório para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório, mas cada subscrição apenas pode confiar num diretório. 

A relação de confiança entre uma subscrição e um diretório é diferente da relação que ela tem com outros recursos no Azure (sites, bases de dados, e assim sucessivamente). Em caso de expiração de uma subscrição, será também interrompido o acesso aos outros recursos associados à subscrição. No entanto, o diretório do Azure AD permanece no Azure e pode associar uma subscrição diferente a esse diretório e gerir o diretório utilizando a nova subscrição.

![diagrama de como as subscrições são associadas](./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png)

Todos os utilizadores têm um diretório raiz único que os autenticam, embora também possam ser convidados noutros diretórios. No Azure AD, pode ver os diretórios dos quais a conta de utilizador é membro ou convidado.

## <a name="azure-ad-and-cloud-service-subscriptions"></a>Azure AD e subscrições de serviços cloud
O Azure AD oferece as principais funcionalidades de gestão de diretórios e de identidades por detrás da maioria dos Cloud Services da Microsoft, incluindo:

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Pode obter o serviço Azure AD gratuitamente quando se inscreve para qualquer um destes serviços cloud da Microsoft. Se pretender adicionar uma subscrição adicional do Azure a um diretório do Azure AD, tem de ter iniciado a sessão com uma conta Microsoft. Se iniciar sessão no Azure com uma conta escolar ou profissional, não é possível adicionar uma subscrição do Azure a um diretório existente, uma vez que a sua conta escolar ou profissional não pode ser autenticada diretamente pelo Azure. 

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>Para adicionar uma subscrição existente ao diretório do Azure AD
Tem de iniciar sessão com uma conta que existe no diretório atual que está associado à subscrição e no diretório ao qual pretende adicionar. 

1. Inicie sessão no [Centro de Contas do Azure](https://account.azure.com/Subscriptions) com uma conta que seja o Administrador de Conta da subscrição cuja propriedade pretende transferir.
2. Confirme que o utilizador que pretende que seja o proprietário da subscrição está no diretório segmentado.
3. Clique em **Transferir subscrição**.
4. Especifique o destinatário. O destinatário recebe automaticamente um e-mail com uma ligação de aceitação.
5. O destinatário clica na ligação e segue as instruções, o que inclui a introdução das respetivas informações de pagamento. Quando o destinatário concluir o processo com êxito, a subscrição é transferida. 
6. O diretório predefinido da subscrição é alterado para o diretório em que o utilizador se encontra.

Para saber mais, consulte [Transferir propriedade da subscrição do Azure para outra conta](../billing/billing-subscription-transfer.md)

## <a name="suggestions-to-manage-both-a-subscription-and-a-directory"></a>Sugestões para gerir uma subscrição e um diretório
As funções administrativas de uma subscrição do Azure gerem os recursos associados à subscrição do Azure. Esta secção explica as diferenças entre os administradores de subscrições do Azure e os administradores de diretórios do Azure AD. As funções administrativas e outras sugestões para utilizá-las para gerir a subscrição são abordadas em [Atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles.md).

Por predefinição, quando se inscreve, é-lhe atribuída a função de Administrador de Serviços. Se outras pessoas tiverem de iniciar sessão e aceder aos serviços com a mesma subscrição, pode adicioná-las como coadministradores. 

O Azure AD tem um conjunto diferente de funções administrativas para gerir o diretório e as funções relacionadas com a identidade. Por exemplo, o administrador global de um diretório pode adicionar utilizadores e grupos no diretório ou exigir o Multifactor Authentication dos utilizadores. É atribuída a função de administrador global a um utilizador que cria um diretório, sendo que poderá atribuir funções administrativas a outros utilizadores. Outros serviços, como o Office 365 e o Microsoft Intune, também recorrem às funções administrativas do Azure AD. 

Os administradores de subscrições do Azure e os administradores de diretórios do Azure AD são duas funções diferentes. 
* Os administradores de subscrições do Azure podem gerir recursos no Azure e podem utilizar o Azure AD no portal do Azure (porque o próprio portal do Azure é um recurso do Azure). 
* Os administradores de diretórios apenas podem gerir propriedades no diretório do Azure AD.

Ambas as funções podem ser atribuídas a uma pessoa, embora tal não seja obrigatório. Um administrador global do diretório poderá não ser selecionado como administrador de serviços ou coadministrador de uma subscrição do Azure, ou vice-versa. Não sendo administrador da subscrição, o utilizador pode iniciar sessão no portal do Azure, mas não consegue gerir os diretórios dessa subscrição no portal. No entanto, este utilizador pode gerir diretórios com outras ferramentas, como o Azure AD PowerShell ou o Centro de Administração do Office 365.

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre como alterar os administradores de uma subscrição do Azure, veja [Transferir a propriedade de uma subscrição do Azure para outra conta](../billing/billing-subscription-transfer.md)
* Para saber mais sobre como o Microsoft Azure controla o acesso aos recursos, consulte [Noções sobre o acesso aos recursos no Azure](active-directory-understanding-resource-access.md)
* Para obter mais informações sobre como atribuir funções no Azure AD, consulte [Atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
