---
title: "Relatórios de segurança na edição Gratuita e Básica do Azure Active Directory | Microsoft Docs"
description: "Lista os vários relatórios disponíveis na pré-visualização do Azure Active Directory"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/19/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: fa5a6dab1e76d62956cbfdd9b8b0f64c014696db
ms.openlocfilehash: c1a3953c79cfac9d6f55b38971ee2b79ff4244eb


---
# <a name="security-reporting-in-the-azure-active-directory-free-and-basic-edition---preview"></a>Relatórios de segurança na edição Gratuita e Básica do Azure Active Directory

Com os relatórios de segurança na [pré-visualização](active-directory-preview-explainer.md) do Azure Active Directory, pode obter informações sobre a probabilidade de contas de utilizador comprometidas no seu ambiente. 

O Azure Active Directory deteta as ações suspeitas relacionadas com as suas contas de utilizador. Para cada ação detetada, é criado um registo denominado *evento de risco*. Para obter mais detalhes, veja [Eventos de risco do Azure Active Directory](active-directory-identity-protection-risk-events.md). 

Os eventos de risco detetados são utilizados para calcular:

- **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. Para obter mais detalhes, veja [Inícios de sessão de risco](active-directory-identityprotection.md#risky-sign-ins). 

- **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. Para obter mais detalhes, veja [Utilizadores sinalizados para risco](active-directory-identityprotection.md#users-flagged-for-risk).  


## <a name="risky-sign-ins-report"></a>Relatório de inícios de sessão de risco

As edições gratuita e básica do Azure Active Directory fornecem uma lista de inícios de sessão de risco detetados que foram comunicados para os seus utilizadores. O relatório de eventos de risco fornece o seguinte:

- **Utilizador** – O nome do utilizador que foi utilizado durante a operação de início de sessão
- **IP** – O endereço IP do dispositivo que foi utilizado para ligar ao Azure Active Directory
- **Localização** – A localização utilizada para ligar ao Azure Active Directory
- **Hora de início de sessão** – A hora quando o início de sessão foi efetuado
- **Estado** – O estado do início de sessão

Este relatório fornece uma opção para transferir os dados do relatório.

![Relatórios](./media/active-directory-reporting-security-azure-portal-free-basic/01.png)

Com base na sua investigação do início de sessão de risco, pode fornecer comentários ao Azure Active Directory nas seguintes ações:

- Resolver
- Marcar como falso positivo
- Ignorar
- Reativar

![Relatórios](./media/active-directory-reporting-security-azure-portal-free-basic/21.png)

Para obter mais detalhes, veja [Fechar eventos de risco manualmente](active-directory-identityprotection.md#closing-risk-events-manually).


## <a name="users-at-risk-report"></a>Relatório de utilizadores em risco

A edição gratuita do Azure Active Directory fornece uma lista de contas de utilizador que podem ter sido comprometidas. 


![Relatórios](./media/active-directory-reporting-security-azure-portal-free-basic/03.png)

Clicar num utilizador da lista abre o painel de dados do utilizador relacionado.
Para os utilizadores que estão em risco, reveja o histórico de início de sessão do utilizador e reponha a palavra-passe, se necessário.

![Relatórios](./media/active-directory-reporting-security-azure-portal-free-basic/46.png)



## <a name="next-steps"></a>Passos seguintes

- Para obter mais detalhes sobre os relatórios do Azure Active Directory, veja o [Guia dos Relatórios do Azure Active Directory](active-directory-reporting-guide.md).
- Para obter mais informações sobre o Azure Active Directory Identity Protection, veja [Azure Active Directory Identity Protection](active-directory-identityprotection.md).




<!--HONumber=Jan17_HO3-->


