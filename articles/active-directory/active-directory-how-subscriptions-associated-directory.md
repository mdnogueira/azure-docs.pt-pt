---
title: "Como adicionar uma subscrição existente do Azure ao diretório do Azure AD | Microsoft Docs"
description: "Como adicionar uma subscrição existente ao diretório do Azure AD"
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
ms.date: 10/17/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: abf207a3ceec708a828170936f7dc7948ccf34a9
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="how-to-add-an-azure-subscription-to-azure-active-directory"></a>Como adicionar uma subscrição do Azure ao Azure Active Directory
Este artigo inclui informações sobre a relação entre a subscrição do Azure e o Azure Active Directory (Azure AD) e como adicionar uma subscrição existente ao diretório do Azure AD. A sua subscrição do Azure tem uma relação de confiança com o Azure AD, o que significa que confia no diretório para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório, mas cada subscrição apenas pode confiar num diretório. 

A relação de confiança entre uma subscrição e um diretório é diferente da relação que ela tem com outros recursos no Azure (sites, bases de dados, e assim sucessivamente). Em caso de expiração de uma subscrição, será também interrompido o acesso aos outros recursos associados à subscrição. No entanto, o diretório do Azure AD permanece no Azure e pode associar uma subscrição diferente a esse diretório e gerir o diretório utilizando a nova subscrição.

Todos os utilizadores têm um diretório raiz único que os autenticam, embora também possam ser convidados noutros diretórios. No Azure AD, pode ver os diretórios dos quais a conta de utilizador é membro ou convidado.

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>Para adicionar uma subscrição existente ao diretório do Azure AD
Tem de iniciar sessão com uma conta que existe no diretório atual que está associado à subscrição e no diretório ao qual pretende adicionar. 

1. Inicie sessão no [Centro de Contas do Azure](https://account.azure.com/Subscriptions) com uma conta que seja o Administrador de Conta da subscrição cuja propriedade pretende transferir.
2. Confirme que o utilizador que pretende que seja o proprietário da subscrição está no diretório segmentado.
3. Clique em **Transferir subscrição**.
4. Especifique o destinatário. O destinatário recebe automaticamente um e-mail com uma ligação de aceitação.
5. O destinatário clica na ligação e segue as instruções, o que inclui a introdução das respetivas informações de pagamento. Quando o destinatário concluir o processo com êxito, a subscrição é transferida. 
6. O diretório predefinido da subscrição é alterado para o diretório em que o utilizador se encontra.

Para obter mais informações, veja [Transferir a propriedade da subscrição do Azure para outra conta](../billing/billing-subscription-transfer.md).

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre como alterar os administradores de uma subscrição do Azure, veja [Transferir a propriedade de uma subscrição do Azure para outra conta](../billing/billing-subscription-transfer.md)
* Para saber mais sobre como o Microsoft Azure controla o acesso aos recursos, consulte [Noções sobre o acesso aos recursos no Azure](active-directory-understanding-resource-access.md)
* Para obter mais informações sobre como atribuir funções no Azure AD, consulte [Atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
