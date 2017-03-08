---
title: "Relatório de atividade de início de sessão no portal do Azure Active Directory - pré-visualização| Microsoft Docs"
description: "Introdução aos relatórios de atividade de início de sessão na pré-visualização do portal do Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 78617dc7e3a4b6eb4fc32d32b6850b8c0832d6d8
ms.openlocfilehash: 9819c5f6a3aea53664d86e3a23b25946c0f2b731
ms.lasthandoff: 02/22/2017


---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal---preview"></a>Relatório de atividade de início de sessão no portal do Azure Active Directory - pré-visualização

Com os relatórios na [pré-visualização](active-directory-preview-explainer.md) do Azure Active Directory, obtém todas as informações de que precisa para determinar o estado de funcionamento do seu ambiente.

A arquitetura de relatórios no Azure Active Directory consiste nos seguintes componentes:

- **Atividade** 
    - **Atividades de início de sessão** – Informações sobre a utilização de aplicações geridas e atividades de início de sessão do utilizador
    - **Registos de auditoria** - informações de atividades do sistema sobre gestão de utilizadores e de grupos, as suas aplicações geridas e atividades de diretório.
- **Segurança** 
    - **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. Para obter mais detalhes, veja Inícios de sessão de risco.
    - **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. Para obter mais detalhes, veja Utilizadores sinalizados para risco.

Este tópico fornece-lhe uma descrição geral das atividades de início de sessão.

## <a name="signs-in-activities"></a>Atividades de início de sessão

Com as informações fornecidas pelo relatório de início de sessão de utilizador, encontrará respostas a perguntas como:

* O que é o padrão de início de sessão de um utilizador?
* Quantos utilizadores iniciaram sessão ao longo de uma semana?
* Qual é o estado destes inícios de sessão?

O ponto de entrada para estes dados é o gráfico de início de sessão de utilizador na secção **Descrição geral** em **Utilizadores e grupos**.

 ![Relatórios](./media/active-directory-reporting-activity-sign-ins/05.png "Relatórios")

O gráfico de início de sessão de utilizador mostra as agregações semanais de inícios de sessão de todos os utilizadores num determinado período de tempo. A predefinição do período de tempo é 30 dias.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/02.png "Relatórios")

Quando clica num dia no gráfico de início de sessão, obtém uma lista detalhada das atividades de início de sessão.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/03.png "Relatórios")

Cada linha na lista de atividades de início de sessão dá-lhe as informações detalhadas sobre o início de sessão selecionado, tais como:

* Quem iniciou sessão?
* Qual era o UPN relacionado?
* Que aplicação foi o destino do início de sessão?
* Qual é o endereço IP do início de sessão?
* Qual era o estado do início de sessão?

## <a name="usage-of-managed-applications"></a>Utilização de aplicações geridas

Com uma vista centrada em aplicações dos seus dados de início de sessão, poderá responder a perguntas como:

* Quem está a utilizar as minhas aplicações?
* Quais são as três aplicações mais utilizadas na sua organização?
* Lancei recentemente uma aplicação. Como está a correr?

O ponto de entrada para estes dados são as três aplicações mais utilizadas na sua organização no relatório dos últimos 30 dias na secção **Descrição geral** em **Aplicações empresariais**.

 ![Relatórios](./media/active-directory-reporting-activity-sign-ins/06.png "Relatórios")

As agregações semanais de inícios de sessão do gráfico de utilização da aplicação das três aplicações mais utilizadas num determinado período de tempo. A predefinição do período de tempo é 30 dias.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/78.png "Relatórios")

Se quiser, pode colocar o foco numa aplicação específica.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Relatórios")

Quando clica num dia no gráfico de utilização da aplicação, obtém uma lista detalhada das atividades de início de sessão.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/top_app_sign_ins.png "Relatórios")

A opção **Inícios de sessão** dá uma visão geral completa de todos os eventos de início de sessão nas suas aplicações.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/85.png "Relatórios")

Utilizando o seletor de colunas, pode selecionar os campos de dados que pretende apresentar.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/column_chooser.png "Relatórios")

## <a name="filtering-sign-ins"></a>Filtrar inícios de sessão
Pode filtrar inícios de sessão para limitar a quantidade de dados apresentados com os campos seguintes:

* Data e hora 
* Nome principal do utilizador
* Nome da aplicação
* Nome do cliente
* Estado de início de sessão

![Relatórios](./media/active-directory-reporting-activity-sign-ins/293.png "Relatórios")

Outro método para filtrar as entradas das atividades de início de sessão consiste em procurar entradas específicas.
O método de pesquisa permite-lhe definir o âmbito dos inícios de sessão em torno de **utilizadores**, **grupos** ou **aplicações** específicos.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/84.png "Relatórios")


## <a name="next-steps"></a>Passos seguintes
Veja o [Guia dos Relatórios do Azure Active Directory](active-directory-reporting-guide.md).


