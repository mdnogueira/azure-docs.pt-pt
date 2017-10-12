---
title: "Relatórios do Azure Active Directory | Microsoft Docs"
description: "Mostra uma descrição geral dos relatórios do Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 738c8f4a56586b87f03973ec258b0a3023affa60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-reporting"></a>Relatórios do Azure Active Directory

Com os relatórios do Azure Active Directory, pode obter informações sobre o desempenho do seu ambiente.  
Os dados fornecidos permite-lhe:

- Determinar de que forma é que os utilizadores utilizam os seus serviços e aplicações
- Detetar potenciais riscos que afetem o estado de funcionamento do ambiente
- Resolver problemas que impedem os utilizadores de trabalhar  

A arquitetura dos relatórios baseia-se em dois pilares principais:

- Relatórios de segurança
- Relatórios de atividade

![Relatórios](./media/active-directory-reporting-azure-portal/01.png)



## <a name="security-reports"></a>Relatórios de segurança

Os relatórios de segurança no Azure Active Directory ajudam-no a proteger as identidades da sua organização.  
Existem dois tipos de relatórios de segurança no Azure Active Directory:

- **Utilizadores sinalizados para risco** - com o [relatório de segurança de utilizadores sinalizados para risco](active-directory-reporting-security-user-at-risk.md), obtém uma descrição geral das contas de utilizador que possam ter sido comprometidas.

- **Inícios de sessão de risco** - com o [relatório de segurança de inícios de sessão de risco](active-directory-reporting-security-risky-sign-ins.md), obtém um indicador para tentativas de início de sessão que possam ter sido realizados por alguém que não seja o legítimo proprietário das contas de utilizador. 

**De que licença do Azure AD precisa para aceder a um relatório de segurança?**  
Todas as edições do Azure Active Directory disponibilizam os relatórios de utilizadores sinalizados para risco e de inícios de sessão de risco.  
No entanto, o nível de granularidade dos relatórios varia entre as edições: 

- Nas **edições Gratuita e Básica do Azure Active Directory**, já tem disponível uma lista dos utilizadores sinalizados para risco e dos inícios de sessão de risco. 

- A edição **Azure Active Directory Premium 1** expande este modelo, permitindo-lhe também examinar alguns dos eventos de risco subjacentes que foram detetados em cada relatório. 

- A edição **do Azure Active Directory Premium 2** proporciona-lhe as informações mais detalhadas sobre os eventos de risco subjacentes e também lhe permite configurar políticas de segurança que respondam automaticamente aos níveis de risco configurados.


## <a name="activity-reports"></a>Relatórios de atividade

Existem dois tipos de relatórios de atividade no Azure Active Directory:

- **Registos de auditoria** - o [relatório de atividade de registos de auditoria](active-directory-reporting-activity-audit-logs.md) dá-lhe acesso ao histórico de cada tarefa executada no seu inquilino.

- **Inícios de sessão** – com o [relatório de atividade de inícios de sessão](active-directory-reporting-activity-sign-ins.md), pode saber quem realizou as tarefas reportadas no relatório de registos de auditoria.



O **relatório de registos de auditoria** disponibiliza-lhe registos das atividades do sistema para efeitos de conformidade.
Entre outras coisas, os dados fornecidos permite-lhe lidar com cenários comuns, como:

- Um utilizador no meu inquilino obteve acesso a um grupo de administração. Quem lhe deu acesso? 

- Quero ver uma lista dos utilizadores que iniciam sessão numa determinada aplicação, porque incluía-a recentemente e quero saber se está a funcionar bem.

- Quero saber quantas reposições de palavras-passe ocorrem no meu inquilino.


**De que licença do Azure AD precisa para aceder aos relatórios de registos de auditoria?**  
O relatório de registos de auditoria está disponível para as funcionalidades para as quais tem licenças. Se tiver uma licença para uma funcionalidade específica, também tem acesso às informações de registo de auditoria relativas à mesma.

Para obter mais detalhes, veja **Comparação das funcionalidades disponíveis nas edições Free, Basic e Premium**, em [Funcionalidades e capacidades do Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features).   



O **relatório de atividade de inícios de sessão** permite-lhe encontrar respostas a perguntas como:

- O que é o padrão de início de sessão de um utilizador?
- Quantos utilizadores iniciaram sessão ao longo de uma semana?
- Qual é o estado destes inícios de sessão?


**De que licença do Azure AD precisa para aceder aos relatórios de atividade de inícios de sessão?**  
Para aceder aos relatórios de atividade de inícios de sessão, o seu inquilino tem de ter uma licença do Azure AD Premium associada ao mesmo.


## <a name="programmatic-access"></a>Acesso programático

Para além da interface de utilizador, os relatórios do Azure Active Directory também lhe fornecem [acesso programático](active-directory-reporting-api-getting-started-azure-portal.md) aos dados dos relatórios. Os dados destes relatórios podem ser bastante úteis para as suas aplicações, como sistemas SIEM e ferramentas de auditoria e de business intelligence. As APIs dos relatórios do Azure AD proporcionam acesso programático aos dados através de um conjunto de APIs baseadas em REST. Pode chamar estas APIs a partir de várias linguagens e ferramentas de programação. 


## <a name="next-steps"></a>Passos seguintes

Se quiser saber mais sobre os vários tipos de relatórios do Azure Active Directory, veja:

- [Relatório de utilizadores sinalizados para risco](active-directory-reporting-security-user-at-risk.md)
- [Relatório de inícios de sessão de risco](active-directory-reporting-security-risky-sign-ins.md)
- [Relatório de registos de auditoria](active-directory-reporting-activity-audit-logs.md)
- [Relatório de registos de inícios de sessão](active-directory-reporting-activity-sign-ins.md)

Se quiser saber mais sobre como aceder aos dados dos relatórios com a API dos relatórios, veja: 

- [Getting started with the Azure Active Directory reporting API](active-directory-reporting-api-getting-started-azure-portal.md) (Introdução à API de relatórios do Azure Active Directory)


<!--Image references-->
[1]: ./media/active-directory-reporting-azure-portal/ic195031.png