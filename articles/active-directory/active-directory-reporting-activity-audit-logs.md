---
title: "Relatório de atividade de auditoria no portal do Azure Active Directory - pré-visualização| Microsoft Docs"
description: "Introdução aos relatórios de atividade de auditoria na pré-visualização do portal do Azure Active Directory"
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
ms.date: 02/21/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 74460492c5eb6edfcc67015f8f6894267cb9edc8
ms.openlocfilehash: 5a219219cd5e34713cd6a1c54a98d6bd15310e05
ms.lasthandoff: 02/22/2017


---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal---preview"></a>Relatório de atividade de auditoria no portal do Azure Active Directory - pré-visualização

Com os relatórios na [pré-visualização](active-directory-preview-explainer.md) do Azure Active Directory, obtém todas as informações de que precisa para determinar o estado de funcionamento do seu ambiente.

A arquitetura de relatórios no Azure Active Directory consiste nos seguintes componentes:

- **Atividade** 
    - **Atividades de início de sessão** – Informações sobre a utilização de aplicações geridas e atividades de início de sessão do utilizador
    - **Registos de auditoria** - informações de atividades do sistema sobre gestão de utilizadores e de grupos, as suas aplicações geridas e atividades de diretório.
- **Segurança** 
    - **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. Para obter mais detalhes, veja Inícios de sessão de risco.
    - **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. Para obter mais detalhes, veja Utilizadores sinalizados para risco.

Este tópico fornece-lhe uma descrição geral das atividades de auditoria.
 
## <a name="audit-logs"></a>Registos de auditoria

Os registos de auditoria no Azure Active Directory fornecem registos das atividades de sistema para efeitos de conformidade.

Existem três categorias principais para fazer a auditoria de atividades relacionadas no portal do Azure:

- Utilizadores e grupos   

- Aplicações

- Diretório   

Para obter uma lista completa de atividades de relatório de auditoria, veja a [lista de eventos de relatório de auditoria](active-directory-reporting-audit-events.md#list-of-audit-report-events).


O ponto de entrada para todos os dados de auditoria é **Registos de auditoria** na secção **Atividade** do **Azure Active Directory**.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/61.png "Registos de auditoria")

Um registo de auditoria tem uma vista de lista que mostra os atores (*quem*), as atividades (*o quê*) e os destinos.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/345.png "Registos de auditoria")

Ao clicar num item na vista de lista, pode obter mais detalhes sobre o mesmo.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/873.png "Registos de auditoria")


## <a name="users-and-groups-audit-logs"></a>Registos de auditoria de utilizadores e grupos

Com os relatórios de auditoria baseados em utilizadores e grupos, poderá obter respostas a perguntas como:

- Que tipos de atualizações os utilizadores aplicaram?

- Quantos utilizadores foram alterados?

- Quantas palavras-passe foram alteradas?

- O que fez um administrador num diretório?

- Quais são os grupos que foram adicionados?

- Existem grupos com as alterações na associação?

- Os proprietários do grupo foram alterados?

- Que licenças foram atribuídas a um grupo ou utilizador?

Se quiser apenas rever dados de auditoria que estejam relacionados com utilizadores e grupos, pode encontrar um vista filtrada em **Registos de auditoria** na secção **Atividade** de **Utilizadores e Grupos**.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/93.png "Registos de auditoria")

## <a name="application-audit-logs"></a>Registos de auditoria de aplicação
Com os relatórios de auditoria baseados em aplicações, poderá obter respostas a perguntas como:

* Quais são as aplicações que foram adicionadas ou atualizadas?
* Quais são as aplicações que foram removidas?
* Um principal de serviço para uma aplicação foi alterado?
* Os nomes das aplicações foram alterados?
* Quem autorizou uma aplicação?

Se quiser apenas rever dados de auditoria que estejam relacionados com aplicações, pode encontrar um vista filtrada em **Registos de auditoria** na secção **Atividade** de **Aplicações empresariais**.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/134.png "Registos de auditoria")

## <a name="filtering-audit-logs"></a>Filtrar registos de auditoria
Pode filtrar inícios de sessão para limitar a quantidade de dados apresentados com os campos seguintes:

- Data e hora

- Nome principal do ator

- Categoria

- Tipo de recurso de atividade

- Atividade

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/625.png "Registos de auditoria")


O filtro de **categoria** permite-lhe restringir o âmbito do relatório de auditoria com base nas seguintes categorias:

- Diretório do Núcleo

- Gestão de Palavra-passe Personalizada

- Gestão de Grupos Personalizada

- Rollover de Palavra-passe Automatizada 

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/626.png "Registos de auditoria")



O conteúdo da lista **Tipo de recurso de atividade**, está associado ao seu ponto de entrada deste painel.  
Se o seu ponto de entrada for o Azure Active Directory, esta lista inclui todos os tipos de atividade possíveis:

- Diretório

- Utilizador

- Grupo 

- Aplicação 

- Política

- Dispositivo


![Auditoria](./media/active-directory-reporting-activity-audit-logs/627.png "Auditoria")

As atividades listadas são delimitadas por tipo de atividade.
Por exemplo, se tiver o **Utilizador** selecionado como **Tipo de Atividade**, a lista **Atividade** só contém atividades relacionadas com grupos.   

![Auditoria](./media/active-directory-reporting-activity-audit-logs/628.png "Auditoria")

Se selecionar **Grupo** como **Tipo de Atividade**, obterá a opção de filtro adicional que também lhe permite filtrar com base nas seguintes **Origens de Atividade**:

- Azure AD

- O365


![Auditoria](./media/active-directory-reporting-activity-audit-logs/629.png "Auditoria")



Outro método para filtrar as entradas de um registo de auditoria consiste em procurar entradas específicas.

![Auditoria](./media/active-directory-reporting-activity-audit-logs/237.png "Auditoria")




## <a name="next-steps"></a>Passos seguintes
Veja o [Guia dos Relatórios do Azure Active Directory](active-directory-reporting-guide.md).


