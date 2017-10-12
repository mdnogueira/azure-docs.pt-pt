---
title: "Relatórios de atividade de auditoria no portal do Azure Active Directory | Microsoft Docs"
description: "Introdução aos relatórios de atividade de auditoria no portal do Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f2d0332d815c82d7d47625e020de2e9c5099deeb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Relatório de atividade de auditoria no portal do Azure Active Directory 

Com os relatórios no Azure Active Directory (Azure AD), obtenha todas as informações de que precisa para determinar o estado de funcionamento do seu ambiente.

A arquitetura de relatórios no Azure AD consiste nos seguintes componentes:

- **Atividade** 
    - **Atividades de início de sessão** – Informações sobre a utilização de aplicações geridas e atividades de início de sessão do utilizador
    - **Registos de auditoria** - informações de atividades do sistema sobre gestão de utilizadores e de grupos, as suas aplicações geridas e atividades de diretório.
- **Segurança** 
    - **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. Para obter mais detalhes, veja Inícios de sessão de risco.
    - **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. Para obter mais detalhes, veja Utilizadores sinalizados para risco.

Este tópico fornece-lhe uma descrição geral das atividades de auditoria.
 
## <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?
* Utilizadores na função de Administrador de Segurança ou de Leitor de Segurança
* Administradores Globais
* Os utilizadores individuais (não administradores) podem ver as suas próprias atividades


## <a name="audit-logs"></a>Registos de auditoria

Os registos de auditoria no Azure Active Directory fornecem registos das atividades de sistema para efeitos de conformidade.  
O primeiro ponto de entrada para todos os dados de auditoria é **Registos de auditoria** na secção **Atividade** do **Azure Active Directory**.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/61.png "Registos de auditoria")

Um registo de auditoria tem uma vista de lista predefinida que mostra:

- a data e hora da ocorrência
- o iniciador / ator (*quem*) de uma atividade 
- a atividade (*o quê*) 
- o destino

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/18.png "Registos de auditoria")

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/19.png "Registos de auditoria")

Isto permite-lhe apresentar campos adicionais ou remover campos que já são apresentados.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/21.png "Registos de auditoria")


Ao clicar num item na vista de lista, obtém todos os detalhes sobre o mesmo.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/22.png "Registos de auditoria")


## <a name="filtering-audit-logs"></a>Filtrar registos de auditoria

Para limitar os dados comunicados a um nível que funcione para si, pode filtrar os dados de auditoria através dos seguintes campos:

- Intervalo de datas
- Iniciado por (Actor)
- Categoria
- Tipo de recurso de atividade
- Atividade

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/23.png "Registos de auditoria")


O filtro **intervalo de datas** permite-lhe definir um período de tempo para os dados devolvidos.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 24 horas
- Personalizado

Quando selecionar um período de tempo personalizado, pode configurar uma hora de início e uma hora de fim.

O filtro **iniciado por** permite-lhe definir o nome de um ator ou o respetivo nome principal universal (UPN).

O filtro **categoria** permite-lhe selecionar um dos seguintes filtros:

- Todos
- Categoria principal
- Diretório principal
- Gestão de palavras-passe personalizada
- Gestão de grupos self-service
- Aprovisionamento da conta - substituição automatizada de palavra-passe
- Utilizadores convidados
- Serviço MIM
- Identity Protection
- B2C

O filtro **tipo de recurso de atividade** permite-lhe selecionar um dos seguintes filtros:

- Todos 
- Grupo
- Diretório
- Utilizador
- Aplicação
- Política
- Dispositivo
- Outros

Quando seleciona **Grupo** como o **tipo de recurso de atividade**, obtém uma categoria de filtro adicional que lhe permite também indicar uma **Origem**:

- Azure AD
- O365


O filtro **atividade** baseia-se na categoria e na seleção de Tipo de recurso de atividade que fizer. Pode selecionar uma atividade específica que queira ver ou selecionar todas. 

Pode obter a lista de todas as Atividades de Auditoria com a Graph API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, onde $tenantdomain é o seu nome de domínio ou ver o artigo [Audit report events](active-directory-reporting-audit-events.md) (Relatório de eventos de auditoria).


## <a name="audit-logs-shortcuts"></a>Atalhos dos registos de auditoria

Para além do **Azure Active Directory**, o portal do Azure proporciona-lhe dois pontos de entrada adicionais para dados de auditoria:

- Utilizadores e grupos
- Aplicações Empresariais

### <a name="users-and-groups-audit-logs"></a>Registos de auditoria de utilizadores e grupos

Com os relatórios de auditoria baseados em utilizadores e grupos, poderá obter respostas a perguntas como:

- Que tipos de atualizações os utilizadores aplicaram?

- Quantos utilizadores foram alterados?

- Quantas palavras-passe foram alteradas?

- O que fez um administrador num diretório?

- Quais são os grupos que foram adicionados?

- Existem grupos com as alterações na associação?

- Os proprietários do grupo foram alterados?

- Que licenças foram atribuídas a um grupo ou utilizador?

Se quiser apenas rever dados de auditoria que estejam relacionados com utilizadores e grupos, pode encontrar um vista filtrada em **Registos de auditoria**, na secção **Atividade** de **Utilizadores e Grupos**. Este ponto de entrada tem **Utilizadores e Grupos** como o **Tipo de Recurso de Atividade** pré-selecionado.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/93.png "Registos de auditoria")

### <a name="enterprise-applications-audit-logs"></a>Registos de auditoria de aplicações empresariais

Com os relatórios de auditoria baseados em aplicações, poderá obter respostas a perguntas como:

* Quais são as aplicações que foram adicionadas ou atualizadas?
* Quais são as aplicações que foram removidas?
* Um principal de serviço para uma aplicação foi alterado?
* Os nomes das aplicações foram alterados?
* Quem autorizou uma aplicação?

Se quiser apenas rever dados de auditoria que estejam relacionados com aplicações, pode encontrar um vista filtrada em **Registos de auditoria**, na secção **Atividade** do painel **Aplicações empresariais**. Este ponto de entrada tem **Aplicações Empresariais** como o **Tipo de Recurso de Atividade** pré-selecionado.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/134.png "Registos de auditoria")

Pode filtrar ainda mais esta vista para apenas **grupos** ou **utilizadores**.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/25.png "Registos de auditoria")


## <a name="next-steps"></a>Passos seguintes

Para obter uma descrição geral dos relatórios, veja [Relatórios do Azure Active Directory](active-directory-reporting-azure-portal.md).

