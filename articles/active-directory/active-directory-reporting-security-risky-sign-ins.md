---
title: "Relatório de inícios de sessão de risco no portal do Azure Active Directory - pré-visualização| Microsoft Docs"
description: "Saiba mais sobre o relatório de inícios de sessão de risco no portal do Azure Active Directory - pré-visualização"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 349109e0c12a1394f96529a94ab884eeb451d242
ms.openlocfilehash: 69b2166dcbc3e4abd99084b47907c90e157791de
ms.lasthandoff: 02/22/2017


---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal---preview"></a>Relatório de inícios de sessão de risco no portal do Azure Active Directory - pré-visualização

Com os relatórios de segurança na [pré-visualização](active-directory-preview-explainer.md) do Azure Active Directory, pode obter informações sobre a probabilidade de contas de utilizador comprometidas no seu ambiente. 

O Azure Active Directory deteta as ações suspeitas relacionadas com as suas contas de utilizador. Para cada ação detetada, é criado um registo denominado *evento de risco*. Para obter mais detalhes, veja [Eventos de risco do Azure Active Directory](active-directory-identity-protection-risk-events.md). 

Os eventos de risco detetados são utilizados para calcular:

- **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. Para obter mais detalhes, veja [Inícios de sessão de risco](active-directory-identityprotection.md#risky-sign-ins). 

- **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. Para obter mais detalhes, veja [Utilizadores sinalizados para risco](active-directory-identityprotection.md#users-flagged-for-risk).  

No portal do Azure, pode encontrar os relatórios de segurança no painel do **Azure Active Directory**, na secção **Segurança**. 

![Inícios de Sessão de Risco](./media/active-directory-reporting-security-risky-sign-ins/10.png)


## <a name="azure-active-directory-free-and-basic-edition"></a>Edição gratuita e básica do Azure Active Directory

As edições gratuita e básica do Azure Active Directory fornecem uma lista de inícios de sessão de risco detetados para os seus utilizadores. O relatório de eventos de risco fornece o seguinte:

- **Utilizador** – O nome do utilizador que foi utilizado durante a operação de início de sessão
- **IP** – O endereço IP do dispositivo que foi utilizado para ligar ao Azure Active Directory
- **Localização** – A localização utilizada para ligar ao Azure Active Directory
- **Hora de início de sessão** – A hora quando o início de sessão foi efetuado
- **Estado** – O estado do início de sessão

Este relatório fornece uma opção para transferir os dados do relatório.

![Inícios de Sessão de Risco](./media/active-directory-reporting-security-risky-sign-ins/01.png)

Com base na sua investigação do início de sessão de risco, pode fornecer comentários ao Azure Active Directory nas seguintes ações:

- Resolver
- Marcar como falso positivo
- Ignorar
- Reativar

![Inícios de Sessão de Risco](./media/active-directory-reporting-security-risky-sign-ins/21.png)

Para obter mais detalhes, veja [Fechar eventos de risco manualmente](active-directory-identityprotection.md#closing-risk-events-manually).

## <a name="azure-active-directory-premium-editions"></a>Edições premium do Azure Active Directory

O relatório de inícios de sessão de risco nas edições premium do Azure Active Directory fornecem-lhe:

- Informações adicionais sobre os [tipos de eventos de risco](active-directory-identity-protection-risk-events.md) que foram detetados

- Uma opção para transferir o relatório


![Inícios de Sessão de Risco](./media/active-directory-reporting-security-risky-sign-ins/456.png)


Ao selecionar um evento de risco, obtém uma vista de relatório detalhado para este evento de risco que lhe permite:

- Uma opção para configurar uma [política de remediação de risco do utilizador](active-directory-identityprotection.md#user-risk-security-policy)  

- Reveja a linha cronológica de deteção para o evento de risco  

- Reveja uma lista de utilizadores para os quais foi detetado este evento de risco

- [Feche manualmente eventos de risco](active-directory-identityprotection.md#closing-risk-events-manually) ou reative um evento de risco fechado manualmente. 


![Inícios de Sessão de Risco](./media/active-directory-reporting-security-risky-sign-ins/457.png)

Ao selecionar um utilizador, obtém uma vista de relatório detalhado para este utilizador que lhe permite:

- Abrir a vista Todos os inícios de sessão

- Repor a palavra-passe do utilizador

- Dispensar todos os eventos

- Investigar os eventos de risco comunicados para o utilizador. 


![Inícios de Sessão de Risco](./media/active-directory-reporting-security-risky-sign-ins/324.png)


Para investigar um evento de risco, selecione um na lista.  
Esta ação abre o painel **Detalhes** para este evento de risco. No painel **Detalhes**, tem a opção de [fechar manualmente um evento de risco](active-directory-identityprotection.md#closing-risk-events-manually) ou reativar um evento de risco fechado manualmente. 


![Inícios de Sessão de Risco](./media/active-directory-reporting-security-risky-sign-ins/325.png)





## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o Azure Active Directory Identity Protection, veja [Azure Active Directory Identity Protection](active-directory-identityprotection.md).


