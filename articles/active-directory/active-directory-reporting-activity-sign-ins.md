---
title: "Relatórios de atividade de início de sessão no portal do Azure Active Directory | Microsoft Docs"
description: "Introdução aos relatórios de atividade de início de sessão no portal do Azure Active Directory"
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
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 0e8ae05b5f8644d4dfd0e6205224cabfb0919531
ms.contentlocale: pt-pt
ms.lasthandoff: 05/26/2017

---
# Relatórios de atividade de início de sessão no portal do Azure Active Directory
<a id="sign-in-activity-reports-in-the-azure-active-directory-portal" class="xliff"></a>

Com os relatórios no Azure Active Directory (Azure AD) no [portal do Azure](https://portal.azure.com) pode obter todas as informações de que precisa para determinar o estado de funcionamento do seu ambiente.

A arquitetura de relatórios no Azure Active Directory consiste nos seguintes componentes:

- **Atividade** 
    - **Atividades de início de sessão** – Informações sobre a utilização de aplicações geridas e atividades de início de sessão do utilizador
    - **Registos de auditoria** - informações de atividades do sistema sobre gestão de utilizadores e de grupos, as suas aplicações geridas e atividades de diretório.
- **Segurança** 
    - **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. Para obter mais detalhes, veja Inícios de sessão de risco.
    - **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. Para obter mais detalhes, veja Utilizadores sinalizados para risco.

Este tópico fornece-lhe uma descrição geral das atividades de início de sessão.

## Pré-requisito
<a id="pre-requisite" class="xliff"></a>

### Quem pode aceder aos dados?
<a id="who-can-access-the-data" class="xliff"></a>
* Utilizadores na função de Administrador de Segurança ou de Leitor de Segurança
* Administradores Globais
* Qualquer utilizador (não administrador) pode aceder aos seus próprios inícios de sessão 

### Que licença do Azure AD precisa para aceder à atividade de entrada?
<a id="what-azure-ad-license-do-you-need-to-access-sign-in-activity" class="xliff"></a>
* O seu inquilino deve ter uma licença do Azure AD Premium associada ao mesmo para ver o relatório de atividade completo de inícios de sessão


## Atividades de início de sessão
<a id="signs-in-activities" class="xliff"></a>

Com as informações fornecidas pelo relatório de início de sessão de utilizador, encontrará respostas a perguntas como:

* O que é o padrão de início de sessão de um utilizador?
* Quantos utilizadores iniciaram sessão ao longo de uma semana?
* Qual é o estado destes inícios de sessão?

O primeiro ponto de entrada de todos os dados de atividades de início de sessão é **Inícios de sessão** na secção de Atividade do **Azure Active**.


![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins/61.png "Atividade de início de sessão")


Um registo de auditoria tem uma vista de lista predefinida que mostra:

- o utilizador relacionado
- a aplicação em que o utilizador tem sessão iniciada
- o estado de início de sessão
- a hora de início de sessão

![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins/41.png "Atividade de início de sessão")

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins/19.png "Atividade de início de sessão")

Isto permite-lhe apresentar campos adicionais ou remover campos que já são apresentados.

![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins/42.png "Atividade de início de sessão")

Ao clicar num item na vista de lista, obtém todos os detalhes sobre o mesmo.

![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins/43.png "Atividade de início de sessão")


## Filtrar atividades de início de sessão
<a id="filtering-sign-in-activities" class="xliff"></a>

Para limitar os dados comunicados para um nível que funcione para si, pode filtrar os dados de inícios de sessão através dos seguintes campos:

- Intervalo de tempo
- Utilizador
- Aplicação
- Cliente
- Estado de início de sessão

![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins/44.png "Atividade de início de sessão")


O filtro **intervalo de tempo** permite-lhe definir um período de tempo para os dados devolvidos.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 24 horas
- Personalizado

Quando selecionar um período de tempo personalizado, pode configurar uma hora de início e uma hora de fim.

O filtro **utilizador** permite-lhe especificar o nome ou o nome principal de utilizador (UPN) do utilizador que mais lhe interessa.

O filtro **aplicação** permite-lhe especificar o nome da aplicação que mais lhe interessa.

O filtro **cliente** permite-lhe especificar informações sobre o dispositivo que mais lhe interessa.

O filtro **estado do início de sessão** permite-lhe selecionar um dos seguintes filtros:

- Todos
- Êxito
- Falha


## Atalhos de atividades de início de sessão
<a id="sign-in-activities-shortcuts" class="xliff"></a>

Para além do Azure Active Directory, o portal do Azure fornece-lhe dois pontos de entrada adicionais para dados de atividades de início de sessão:

- Utilizadores e grupos
- Aplicações Empresariais


### Atividades de inícios de sessão de utilizadores e grupos
<a id="users-and-groups-sign-ins-activities" class="xliff"></a>

Com as informações fornecidas pelo relatório de início de sessão de utilizador, encontrará respostas a perguntas como:

- O que é o padrão de início de sessão de um utilizador?
- Quantos utilizadores iniciaram sessão ao longo de uma semana?
- Qual é o estado destes inícios de sessão?



O ponto de entrada para estes dados é o gráfico de início de sessão de utilizador na secção **Descrição geral** em **Utilizadores e grupos**.

![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins/45.png "Atividade de início de sessão")

O gráfico de início de sessão de utilizador mostra as agregações semanais de inícios de sessão de todos os utilizadores num determinado período de tempo. A predefinição do período de tempo é 30 dias.

![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins/46.png "Atividade de início de sessão")

Quando clica num dia no gráfico de início de sessão, obtém uma lista detalhada das atividades de início de sessão para este dia.

![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins/41.png "Atividade de início de sessão")

Cada linha na lista de atividades de início de sessão dá-lhe as informações detalhadas sobre o início de sessão selecionado, tais como:

* Quem iniciou sessão?
* Qual era o UPN relacionado?
* Que aplicação foi o destino do início de sessão?
* Qual é o endereço IP do início de sessão?
* Qual era o estado do início de sessão?

A opção **Inícios de sessão** oferece uma descrição geral completa de todos os inícios de sessão dos utilizadores.

![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins/51.png "Atividade de início de sessão")



## Utilização de aplicações geridas
<a id="usage-of-managed-applications" class="xliff"></a>

Com uma vista centrada em aplicações dos seus dados de início de sessão, poderá responder a perguntas como:

* Quem está a utilizar as minhas aplicações?
* Quais são as três aplicações mais utilizadas na sua organização?
* Lancei recentemente uma aplicação. Como está a correr?

O ponto de entrada para estes dados são as três aplicações mais utilizadas na sua organização no relatório dos últimos 30 dias na secção **Descrição geral** em **Aplicações empresariais**.

![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins/64.png "Atividade de início de sessão")

As agregações semanais de inícios de sessão do gráfico de utilização da aplicação das três aplicações mais utilizadas num determinado período de tempo. A predefinição do período de tempo é 30 dias.

![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins/47.png "Atividade de início de sessão")

Se quiser, pode colocar o foco numa aplicação específica.


![Relatórios](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Relatórios")

Quando clica num dia no gráfico de utilização da aplicação, obtém uma lista detalhada das atividades de início de sessão.


![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins/48.png "Atividade de início de sessão")


A opção **Inícios de sessão** dá uma visão geral completa de todos os eventos de início de sessão nas suas aplicações.

![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins/49.png "Atividade de início de sessão")



## Passos seguintes
<a id="next-steps" class="xliff"></a>
Veja o [Guia dos Relatórios do Azure Active Directory](active-directory-reporting-guide.md).


