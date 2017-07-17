---
title: "Utilizadores no relatório de segurança de risco no portal do Azure Active Directory | Microsoft Docs"
description: "Saiba mais sobre os utilizadores no relatório de segurança de risco no portal do Azure Active Directory"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 01ecb98c02b2a01007c7f76805d4db4b7aeee1f0
ms.contentlocale: pt-pt
ms.lasthandoff: 05/08/2017

---
# Utilizadores no relatório de segurança de risco no portal do Azure Active Directory
<a id="users-at-risk-security-report-in-the-azure-active-directory-portal" class="xliff"></a>

Com os relatórios de segurança no Azure Active Directory (Azure AD), pode obter informações sobre a probabilidade de contas de utilizador comprometidas no seu ambiente. 

O Azure Active Directory deteta as ações suspeitas relacionadas com as suas contas de utilizador. Para cada ação detetada, é criado um registo denominado *evento de risco*. Para obter mais detalhes, veja [Eventos de risco do Azure Active Directory](active-directory-identity-protection-risk-events.md). 

Os eventos de risco detetados são utilizados para calcular:

- **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. Para obter mais detalhes, veja [Inícios de sessão de risco](active-directory-identityprotection.md#risky-sign-ins). 

- **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. Para obter mais detalhes, veja [Utilizadores sinalizados para risco](active-directory-identityprotection.md#users-flagged-for-risk).  

No portal do Azure, pode encontrar os relatórios de segurança no painel do **Azure Active Directory**, na secção **Segurança**.  

![Inícios de Sessão de Risco](./media/active-directory-reporting-security-user-at-risk/10.png)

## Edição gratuita e básica do Azure Active Directory
<a id="azure-active-directory-free-and-basic-edition" class="xliff"></a>

Os utilizadores no relatório de risco nas edições gratuita e básica do Azure Active Directory fornecem uma lista de contas de utilizador que podem ter sido comprometidas. 


![Inícios de Sessão de Risco](./media/active-directory-reporting-security-user-at-risk/03.png)

Selecionar um utilizador abre o painel de dados do utilizador relacionado.
Para os utilizadores que estão em risco, pode rever o histórico de início de sessão do utilizador e repor a palavra-passe, se necessário.

![Inícios de Sessão de Risco](./media/active-directory-reporting-security-user-at-risk/46.png)

## Edições premium do Azure Active Directory
<a id="azure-active-directory-premium-editions" class="xliff"></a>

Os utilizadores no relatório de risco nas edições premium do Azure Active Directory fornecem-lhe:

- Uma [lista de contas de utilizador](active-directory-identityprotection.md#users-flagged-for-risk) que poderão ter sido comprometidas 

- Informações adicionais sobre os [tipos de eventos de risco](active-directory-identity-protection-risk-events.md) que foram detetados

- Uma opção para transferir o relatório

- Uma opção para configurar uma [política de remediação de risco do utilizador](active-directory-identityprotection.md#user-risk-security-policy)  


![Inícios de Sessão de Risco](./media/active-directory-reporting-security-user-at-risk/71.png)

Ao selecionar um utilizador, obtém uma vista de relatório detalhado para este utilizador que lhe permite:

- Abrir a vista Todos os inícios de sessão

- Repor a palavra-passe do utilizador

- Dispensar todos os eventos

- Investigar os eventos de risco comunicados para o utilizador. 


![Inícios de Sessão de Risco](./media/active-directory-reporting-security-user-at-risk/324.png)


Para investigar um evento de risco, selecione um na lista.  
Esta ação abre o painel **Detalhes** para este evento de risco. No painel **Detalhes**, tem a opção de [fechar manualmente um evento de risco](active-directory-identityprotection.md#closing-risk-events-manually) ou reativar um evento de risco fechado manualmente. 


![Inícios de Sessão de Risco](./media/active-directory-reporting-security-user-at-risk/325.png)



## Passos seguintes
<a id="next-steps" class="xliff"></a>

- Para obter mais informações sobre o Azure Active Directory Identity Protection, veja [Azure Active Directory Identity Protection](active-directory-identityprotection.md).


