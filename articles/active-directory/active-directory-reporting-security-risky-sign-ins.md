---
title: "Relatório de inícios de sessão de risco no portal do Azure Active Directory | Microsoft Docs"
description: "Saiba mais sobre o relatório de inícios de sessão de risco no portal do Azure Active Directory"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 45a6f63bd920c9a70c25b8dfae084ea030256cf4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Relatório de inícios de sessão de risco no portal do Azure Active Directory

Com os relatórios de segurança no Azure Active Directory (Azure AD), pode obter informações sobre a probabilidade de contas de utilizador comprometidas no seu ambiente. 

O Azure AD deteta as ações suspeitas relacionadas com as suas contas de utilizador. Para cada ação detetada, é criado um registo denominado *evento de risco*. Para obter mais detalhes, veja [Eventos de risco do Azure Active Directory](active-directory-identity-protection-risk-events.md). 

Os eventos de risco detetados são utilizados para calcular:

- **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. Para obter mais detalhes, veja [Inícios de sessão de risco](active-directory-identityprotection.md#risky-sign-ins). 

- **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. Para obter mais detalhes, veja [Utilizadores sinalizados para risco](active-directory-identityprotection.md#users-flagged-for-risk).  

No [portal do Azure](https://portal.azure.com), pode encontrar os relatórios de segurança no painel do **Azure Active Directory**, na secção **Segurança**. 

![Inícios de Sessão de Risco](./media/active-directory-reporting-security-risky-sign-ins/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>De que licença do Azure AD precisa para aceder a um relatório de segurança?  

Todas as edições do Azure Active Directory disponibilizam os relatórios de inícios de sessão de risco.  
No entanto, o nível de granularidade dos relatórios varia entre as edições: 

- Nas **edições Gratuita e Básica do Azure Active Directory**, já tem disponível uma lista dos inícios de sessão de risco. 

- A edição **Azure Active Directory Premium 1** expande este modelo, permitindo-lhe também examinar alguns dos eventos de risco subjacentes que foram detetados em cada relatório. 

- A edição **do Azure Active Directory Premium 2** proporciona-lhe as informações mais detalhadas sobre todos os eventos de risco subjacentes e também lhe permite configurar políticas de segurança que respondam automaticamente aos níveis de risco configurados.



## <a name="azure-active-directory-free-and-basic-edition"></a>Edição gratuita e básica do Azure Active Directory

As edições gratuita e básica do Azure Active Directory fornecem uma lista de inícios de sessão de risco detetados para os seus utilizadores. Este relatório lista:

- **Utilizador** – O nome do utilizador que foi utilizado durante a operação de início de sessão
- **IP** – O endereço IP do dispositivo que foi utilizado para ligar ao Azure Active Directory
- **Localização** – A localização utilizada para ligar ao Azure Active Directory
- **Hora de início de sessão** – A hora quando o início de sessão foi efetuado
- **Estado** – O estado do início de sessão


![Inícios de Sessão de Risco](./media/active-directory-reporting-security-risky-sign-ins/01.png)

Com base na sua investigação do início de sessão de risco, pode fornecer comentários ao Azure Active Directory nas seguintes ações:

- Resolver
- Marcar como falso positivo
- Ignorar
- Reativar

![Inícios de Sessão de Risco](./media/active-directory-reporting-security-risky-sign-ins/21.png)

Para obter mais detalhes, veja [Fechar eventos de risco manualmente](active-directory-identityprotection.md#closing-risk-events-manually).

Este relatório disponibiliza uma opção para:

- Pesquisar recursos
- Transferir os dados do relatório


![Inícios de Sessão de Risco](./media/active-directory-reporting-security-risky-sign-ins/93.png)


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

