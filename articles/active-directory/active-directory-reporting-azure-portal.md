---
title: "Relatórios do Azure Active Directory - pré-visualização | Microsoft Docs"
description: "Lista os vários relatórios disponíveis na pré-visualização do Azure Active Directory"
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
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: be986fd7bb1745dcf43a1066dfabc1e1c699ab4c
ms.openlocfilehash: b9cd11954a52600c1cd50155cb7ce9b7d2355cd3
ms.lasthandoff: 01/20/2017


---
# <a name="azure-active-directory-reporting---preview"></a>Relatórios do Azure Active Directory - pré-visualização


*Esta documentação faz parte do [Guia dos Relatórios do Azure Active Directory](active-directory-reporting-guide.md).*

Com os relatórios na pré-visualização do Azure Active Directory, obtém todas as informações de que precisa para determinar o estado de funcionamento do seu ambiente. [O que inclui a pré-visualização?](active-directory-preview-explainer.md)

Existem duas áreas principais de relatórios:

* **Atividades de início de sessão** – Informações sobre a utilização de aplicações geridas e atividades de início de sessão do utilizador
* **Registos de auditoria** - informações de atividades do sistema sobre gestão de utilizadores e de grupos, as suas aplicações geridas e atividades de diretório

Consoante o âmbito dos dados de que está à procura, pode aceder a estes relatórios clicando em **Utilizadores e grupos** ou em **Aplicações empresariais** na lista de serviços no [portal do Azure](https://portal.azure.com).

## <a name="sign-in-activities"></a>Atividades de início de sessão
### <a name="user-sign-in-activities"></a>Atividades de início de sessão do utilizador
Com as informações fornecidas pelo relatório de início de sessão de utilizador, encontrará respostas a perguntas como:

* O que é o padrão de início de sessão de um utilizador?
* Quantos utilizadores iniciaram sessão ao longo de uma semana?
* Qual é o estado destes inícios de sessão?

O ponto de entrada para estes dados é o gráfico de início de sessão de utilizador na secção **Descrição geral** em **Utilizadores e grupos**.

 ![Relatórios](./media/active-directory-reporting-azure-portal/05.png "Relatórios")

O gráfico de início de sessão de utilizador mostra as agregações semanais de inícios de sessão de todos os utilizadores num determinado período de tempo. A predefinição do período de tempo é 30 dias.

![Relatórios](./media/active-directory-reporting-azure-portal/02.png "Relatórios")

Quando clica num dia no gráfico de início de sessão, obtém uma lista detalhada das atividades de início de sessão.

![Relatórios](./media/active-directory-reporting-azure-portal/03.png "Relatórios")

Cada linha na lista de atividades de início de sessão dá-lhe as informações detalhadas sobre o início de sessão selecionado, tais como:

* Quem iniciou sessão?
* Qual era o UPN relacionado?
* Que aplicação foi o destino do início de sessão?
* Qual é o endereço IP do início de sessão?
* Qual era o estado do início de sessão?

### <a name="usage-of-managed-applications"></a>Utilização de aplicações geridas
Com uma vista centrada em aplicações dos seus dados de início de sessão, poderá responder a perguntas como:

* Quem está a utilizar as minhas aplicações?
* Quais são as três aplicações mais utilizadas na sua organização?
* Lancei recentemente uma aplicação. Como está a correr?

O ponto de entrada para estes dados são as três aplicações mais utilizadas na sua organização no relatório dos últimos 30 dias na secção **Descrição geral** em **Aplicações empresariais**.

 ![Relatórios](./media/active-directory-reporting-azure-portal/06.png "Relatórios")

As agregações semanais de inícios de sessão do gráfico de utilização da aplicação das três aplicações mais utilizadas num determinado período de tempo. A predefinição do período de tempo é 30 dias.

![Relatórios](./media/active-directory-reporting-azure-portal/78.png "Relatórios")

Se quiser, pode colocar o foco numa aplicação específica.

![Relatórios](./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Relatórios")

Quando clica num dia no gráfico de utilização da aplicação, obtém uma lista detalhada das atividades de início de sessão.

![Relatórios](./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Relatórios")

A opção **Inícios de sessão** dá uma visão geral completa de todos os eventos de início de sessão nas suas aplicações.

![Relatórios](./media/active-directory-reporting-azure-portal/85.png "Relatórios")

Utilizando o seletor de colunas, pode selecionar os campos de dados que pretende apresentar.

![Relatórios](./media/active-directory-reporting-azure-portal/column_chooser.png "Relatórios")

### <a name="filtering-sign-ins"></a>Filtrar inícios de sessão
Pode filtrar inícios de sessão para limitar a quantidade de dados apresentados com os campos seguintes:

* Data e hora 
* Nome principal do utilizador
* Nome da aplicação
* Nome do cliente
* Estado de início de sessão

![Relatórios](./media/active-directory-reporting-azure-portal/293.png "Relatórios")

Outro método para filtrar as entradas das atividades de início de sessão consiste em procurar entradas específicas.
O método de pesquisa permite-lhe definir o âmbito dos inícios de sessão em torno de **utilizadores**, **grupos** ou **aplicações** específicos.

![Relatórios](./media/active-directory-reporting-azure-portal/84.png "Relatórios")

## <a name="audit-logs"></a>Registos de auditoria
Os registos de auditoria no Azure Active Directory fornecem registos das atividades de sistema para efeitos de conformidade.

Existem três categorias principais para fazer a auditoria de atividades relacionadas no portal do Azure:

* Utilizadores e grupos   
* Aplicações
* Diretório   

Para obter uma lista completa de atividades de relatório de auditoria, veja a [lista de eventos de relatório de auditoria](active-directory-reporting-audit-events.md#list-of-audit-report-events).

O ponto de entrada para todos os dados de auditoria é **Registos de auditoria** na secção **Atividade** do **Azure Active Directory**.

![Auditoria](./media/active-directory-reporting-azure-portal/61.png "Auditoria")

Um registo de auditoria tem uma vista de lista que mostra os atores (quem), as atividades (o quê) e os destinos.

![Auditoria](./media/active-directory-reporting-azure-portal/345.png "Auditoria")

Ao clicar num item na vista de lista, pode obter mais detalhes sobre o mesmo.

![Auditoria](./media/active-directory-reporting-azure-portal/873.png "Auditoria")

### <a name="users-and-groups-audit-logs"></a>Registos de auditoria de utilizadores e grupos
Com os relatórios de auditoria baseados em utilizadores e grupos, poderá obter respostas a perguntas como:

* Que tipos de atualizações os utilizadores aplicaram?
* Quantos utilizadores foram alterados?
* Quantas palavras-passe foram alteradas?
* O que fez um administrador num diretório?
* Quais são os grupos que foram adicionados?
* Existem grupos com as alterações na associação?
* Os proprietários do grupo foram alterados?
* Que licenças foram atribuídas a um grupo ou utilizador?

Se quiser apenas rever dados de auditoria que estejam relacionados com utilizadores e grupos, pode encontrar um vista filtrada em **Registos de auditoria** na secção **Atividade** de **Utilizadores e Grupos**.

![Auditoria](./media/active-directory-reporting-azure-portal/93.png "Auditoria")

### <a name="application-audit-logs"></a>Registos de auditoria de aplicação
Com os relatórios de auditoria baseados em aplicações, poderá obter respostas a perguntas como:

* Quais são as aplicações que foram adicionadas ou atualizadas?
* Quais são as aplicações que foram removidas?
* Um principal de serviço para uma aplicação foi alterado?
* Os nomes das aplicações foram alterados?
* Quem autorizou uma aplicação?

Se quiser apenas rever dados de auditoria que estejam relacionados com aplicações, pode encontrar um vista filtrada em **Registos de auditoria** na secção **Atividade** de **Aplicações empresariais**.

![Auditoria](./media/active-directory-reporting-azure-portal/134.png "Auditoria")

### <a name="filtering-audit-logs"></a>Filtrar registos de auditoria
Pode filtrar inícios de sessão para limitar a quantidade de dados apresentados com os campos seguintes:

* Data e hora
* Nome principal do ator
* Tipo de atividade
* Atividade

![Auditoria](./media/active-directory-reporting-azure-portal/356.png "Auditoria")

O conteúdo da lista **Tipo de Atividade**, está associado ao seu ponto de entrada deste painel.  
Se o seu ponto de entrada for o Azure Active Directory, esta lista inclui todos os tipos de atividade possíveis:

* Aplicação 
* Grupo 
* Utilizador
* Dispositivo
* Diretório
* Política
* Outros

![Auditoria](./media/active-directory-reporting-azure-portal/825.png "Auditoria")

As atividades listadas são delimitadas por tipo de atividade.
Por exemplo, se tiver o **Grupo** selecionado como **Tipo de Atividade**, a lista **Atividade** só contém atividades relacionadas com grupos.   

![Auditoria](./media/active-directory-reporting-azure-portal/654.png "Auditoria")

Outro método para filtrar as entradas de um registo de auditoria consiste em procurar entradas específicas.

![Auditoria](./media/active-directory-reporting-azure-portal/237.png "Auditoria")

## <a name="next-steps"></a>Passos seguintes
Veja o [Guia dos Relatórios do Azure Active Directory](active-directory-reporting-guide.md).


