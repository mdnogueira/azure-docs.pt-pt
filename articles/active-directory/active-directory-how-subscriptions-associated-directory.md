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
ms.date: 08/22/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: b2f8cc774b6cc245a4000e5c6924a8bb817707bc
ms.contentlocale: pt-pt
ms.lasthandoff: 08/24/2017

---
# <a name="how-azure-subscriptions-are-associated-with-azure-active-directory"></a>Como as subscrições do Azure estão associadas ao Azure Active Directory
Este artigo inclui informações sobre a relação entre uma subscrição do Azure e o Azure Active Directory (Azure AD).

## <a name="your-azure-subscriptions-relationship-to-azure-ad"></a>Relação da sua subscrição do Azure com o Azure AD
A sua subscrição do Azure tem uma relação de confiança com o Azure AD, o que significa que confia no diretório para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório, mas cada subscrição apenas pode confiar num diretório. 

A relação de confiança entre uma subscrição e um diretório é diferente da relação que ela tem com outros recursos no Azure (sites, bases de dados, e assim sucessivamente). Em caso de expiração de uma subscrição, será também interrompido o acesso aos outros recursos associados à subscrição. No entanto, o diretório permanece no Azure e pode associar uma subscrição diferente a esse diretório e continuar a gerir os utilizadores do diretório.

![diagrama de como as subscrições são associadas](./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png)

O Azure AD não funciona como os outros serviços na sua subscrição do Azure. Outros serviços do Azure estão subordinados à subscrição do Azure. Mas o que vê no Azure AD não varia consoante a subscrição. Permite o acesso a diretórios com base no utilizador com sessão iniciada.

Todos os utilizadores têm um diretório raiz único que os autenticam, embora também possam ser convidados noutros diretórios. No Azure AD, apenas pode ver os diretórios dos quais a conta de utilizador é membro. Um diretório também pode ser sincronizado com o Active Directory no local.

## <a name="azure-ad-and-cloud-service-subscriptions"></a>Azure AD e subscrições de serviços cloud
O Azure AD oferece as principais funcionalidades de gestão de diretórios e de identidades por detrás da maioria dos Cloud Services da Microsoft, incluindo:

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Pode obter o serviço Azure AD gratuitamente quando se inscreve para qualquer um destes serviços cloud da Microsoft. Se pretender adicionar uma subscrição adicional do Azure a um diretório do Azure AD, só pode fazê-lo se tiver iniciado a sessão com uma conta Microsoft. Por exemplo, caso pretenda inscrever-se no Azure com uma conta Microsoft e inscrever-se também num serviço cloud diferente da Microsoft através de uma conta escolar ou profissional, tem duas instâncias do Azure AD:
1. O diretório predefinido da sua subscrição do Azure. Pode adicionar outra subscrição do Azure a este diretório ou a outros que criar, se tiver iniciado sessão com uma conta Microsoft, porque pode ser autenticado pelo Azure.
2. O diretório de raiz da sua conta escolar ou profissional. Se tiver iniciado sessão no Azure com uma conta escolar ou profissional, não é possível adicionar uma subscrição do Azure a um diretório existente, uma vez que a sua conta escolar ou profissional não pode ser autenticada diretamente pelo Azure. 
 
Para obter mais informações sobre como alterar os administradores de uma subscrição do Azure, veja [Transferir a propriedade de uma subscrição do Azure para outra conta](../billing/billing-subscription-transfer.md)

## <a name="suggestions-to-manage-both-a-subscription-and-a-directory"></a>Sugestões para gerir uma subscrição e um diretório
As funções administrativas de uma subscrição do Azure gerem os recursos associados à subscrição do Azure. Esta secção explica as diferenças entre os administradores de subscrições do Azure e os administradores de diretórios do Azure AD. As funções administrativas e outras sugestões para utilizá-las para gerir a subscrição são abordadas em [Atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles.md).

Por predefinição, quando se inscreve, é-lhe atribuída a função de Administrador de Serviços. Se outras pessoas tiverem de iniciar sessão e aceder aos serviços com a mesma subscrição, pode adicioná-las como coadministradores. 

O Azure AD tem um conjunto diferente de funções administrativas para gerir o diretório e as funções relacionadas com a identidade. Por exemplo, o administrador global de um diretório pode adicionar utilizadores e grupos no diretório ou exigir o Multifactor Authentication dos utilizadores. É atribuída a função de administrador global a um utilizador que cria um diretório, sendo que poderá atribuir funções administrativas a outros utilizadores. Outros serviços, como o Office 365 e o Microsoft Intune, também recorrem às funções administrativas do Azure AD. 

Os administradores de subscrições do Azure e os administradores de diretórios do Azure AD são duas funções diferentes. 
* Os administradores de subscrições do Azure podem gerir recursos no Azure e podem utilizar o Azure AD no portal do Azure (porque o próprio portal do Azure é um recurso do Azure). 
* Os administradores de diretórios apenas podem gerir propriedades no diretório do Azure AD.

Ambas as funções podem ser atribuídas a uma pessoa, embora tal não seja obrigatório. Pode ser atribuída a função de administrador global do diretório a um utilizador, sem lhe atribuir a função de administrador de serviços ou coadministrador de uma subscrição do Azure. Não sendo administrador da subscrição, o utilizador pode iniciar sessão no portal do Azure, mas não consegue gerir os diretórios dessa subscrição no portal. Este utilizador pode gerir diretórios com outras ferramentas, como o Azure AD PowerShell ou o Centro de Administração do Office 365.

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre como alterar os administradores de uma subscrição do Azure, veja [Transferir a propriedade de uma subscrição do Azure para outra conta](../billing/billing-subscription-transfer.md)
* Para saber mais sobre como o Microsoft Azure controla o acesso aos recursos, consulte [Noções sobre o acesso aos recursos no Azure](active-directory-understanding-resource-access.md)
* Para obter mais informações sobre como atribuir funções no Azure AD, consulte [Atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

