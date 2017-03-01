---
title: "Utilizadores no relatório de segurança de risco no portal do Azure Active Directory | Microsoft Docs"
description: "Saiba mais sobre os utilizadores no relatório de segurança de risco no portal do Azure Active Directory - pré-visualização"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 349109e0c12a1394f96529a94ab884eeb451d242
ms.openlocfilehash: 48c504a9ed5bc4ef9f0bff889df031962c5bf6e8
ms.lasthandoff: 02/22/2017


---
# <a name="users-at-risk-security-report-in-the-azure-active-directory-portal---preview"></a>Utilizadores no relatório de segurança de risco no portal do Azure Active Directory - pré-visualização

Com os relatórios de segurança na [pré-visualização](active-directory-preview-explainer.md) do Azure Active Directory, pode obter informações sobre a probabilidade de contas de utilizador comprometidas no seu ambiente. 

O Azure Active Directory deteta as ações suspeitas relacionadas com as suas contas de utilizador. Para cada ação detetada, é criado um registo denominado *evento de risco*. Para obter mais detalhes, veja [Eventos de risco do Azure Active Directory](active-directory-identity-protection-risk-events.md). 

Os eventos de risco detetados são utilizados para calcular:

- **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. Para obter mais detalhes, veja [Inícios de sessão de risco](active-directory-identityprotection.md#risky-sign-ins). 

- **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. Para obter mais detalhes, veja [Utilizadores sinalizados para risco](active-directory-identityprotection.md#users-flagged-for-risk).  

No portal do Azure, pode encontrar os relatórios de segurança no painel do **Azure Active Directory**, na secção **Segurança**.  

![Inícios de Sessão de Risco](./media/active-directory-reporting-security-user-at-risk/10.png)

## <a name="azure-active-directory-free-and-basic-edition"></a>Edição gratuita e básica do Azure Active Directory

Os utilizadores no relatório de risco nas edições gratuita e básica do Azure Active Directory fornecem uma lista de contas de utilizador que podem ter sido comprometidas. 


![Inícios de Sessão de Risco](./media/active-directory-reporting-security-user-at-risk/03.png)

Selecionar um utilizador abre o painel de dados do utilizador relacionado.
Para os utilizadores que estão em risco, pode rever o histórico de início de sessão do utilizador e repor a palavra-passe, se necessário.

![Inícios de Sessão de Risco](./media/active-directory-reporting-security-user-at-risk/46.png)

## <a name="azure-active-directory-premium-editions"></a>Edições premium do Azure Active Directory

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



## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o Azure Active Directory Identity Protection, veja [Azure Active Directory Identity Protection](active-directory-identityprotection.md).


