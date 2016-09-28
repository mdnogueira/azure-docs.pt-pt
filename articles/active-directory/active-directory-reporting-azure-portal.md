<properties
   pageTitle="Relatórios do Azure Active Directory - pré-visualização | Microsoft Azure"
   description="Lista os vários relatórios disponíveis na pré-visualização do Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="markusvi"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/12/2016"
   ms.author="markvi"/>


# Relatórios do Azure Active Directory - pré-visualização

*Esta documentação faz parte do [Guia dos Relatórios do Azure Active Directory](active-directory-reporting-guide.md).*

Com os relatórios na pré-visualização do Azure Active Directory, obtém todas as informações de que precisa para determinar o estado de funcionamento do seu ambiente. [O que inclui a pré-visualização?](active-directory-preview-explainer.md)

Existem duas áreas principais de relatórios:

- **Atividades de início de sessão** – Informações sobre a utilização de aplicações geridas e atividades de início de sessão do utilizador

- **Atividades de sistema** - Informações de auditoria sobre gestão de utilizadores e de grupos, as suas aplicações geridas e atividades de diretório

Consoante o âmbito dos dados de que está à procura, pode aceder a estes relatórios clicando em **Utilizadores e grupos** ou em **Aplicações empresariais** na lista de serviços no [portal do Azure](https://portal.azure.com).

## Atividades de início de sessão

### Atividades de início de sessão do utilizador

Com as informações fornecidas pelo relatório de início de sessão de utilizador, encontrará respostas a perguntas como:

- O que é o padrão de início de sessão de um utilizador?
- Quantos utilizadores iniciaram sessão ao longo de uma semana?
- Qual é o estado destes inícios de sessão?

O ponto de entrada para estes dados é o gráfico de início de sessão de utilizador na secção **Descrição geral** em **Utilizadores e grupos**.

 ![Relatórios](./media/active-directory-reporting-azure-portal/05.png "Reporting")

O gráfico de início de sessão de utilizador mostra as agregações semanais de inícios de sessão de todos os utilizadores num determinado período de tempo. A predefinição do período de tempo é 30 dias.

![Relatórios](./media/active-directory-reporting-azure-portal/02.png "Reporting")

Quando clica num dia no gráfico de início de sessão, obtém uma lista detalhada das atividades de início de sessão.

![Relatórios](./media/active-directory-reporting-azure-portal/03.png "Reporting")

Cada linha na lista de atividades de início de sessão dá-lhe as informações detalhadas sobre o início de sessão selecionado, tais como:

- Quem iniciou sessão?

- Qual era o UPN relacionado?

- Que aplicação foi o destino do início de sessão?

- Qual é o endereço IP do início de sessão?

- Qual era o estado do início de sessão?

### Utilização de aplicações geridas

Com uma vista centrada em aplicações dos seus dados de início de sessão, poderá responder a perguntas como:

- Quem está a utilizar as minhas aplicações?

- Quais são as três aplicações mais utilizadas na sua organização?

- Lancei recentemente uma aplicação. Como está a correr?


O ponto de entrada para estes dados são as três aplicações mais utilizadas na sua organização no relatório dos últimos 30 dias na secção **Descrição geral** em **Aplicações empresariais**.

 ![Relatórios](./media/active-directory-reporting-azure-portal/06.png "Reporting")


As agregações semanais de inícios de sessão do gráfico de utilização da aplicação das três aplicações mais utilizadas num determinado período de tempo. A predefinição do período de tempo é 30 dias.

![Relatórios](./media/active-directory-reporting-azure-portal/78.png "Reporting")

Se quiser, pode colocar o foco numa aplicação específica.

![Relatórios](./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Reporting")


Quando clica num dia no gráfico de utilização da aplicação, obtém uma lista detalhada das atividades de início de sessão.


![Relatórios](./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Reporting")



A opção **Inícios de sessão** dá uma visão geral completa de todos os eventos de início de sessão nas suas aplicações.

![Relatórios](./media/active-directory-reporting-azure-portal/85.png "Reporting")

Utilizando o seletor de colunas, pode selecionar os campos de dados que pretende apresentar.

![Relatórios](./media/active-directory-reporting-azure-portal/column_chooser.png "Reporting")



### Filtrar inícios de sessão

Pode filtrar inícios de sessão por um intervalo de tempo para limitar a quantidade de dados apresentados.

![Relatórios](./media/active-directory-reporting-azure-portal/927.png "Reporting")


Outro método para filtrar as entradas das atividades de início de sessão consiste em procurar entradas específicas.
O método de pesquisa permite-lhe definir o âmbito dos inícios de sessão em torno de **utilizadores**, **grupos** ou **aplicações** específicos.


![Relatórios](./media/active-directory-reporting-azure-portal/84.png "Reporting")

## Atividades de sistema

Os registos de auditoria no Azure Active Directory fornecem registos das atividades de sistema para efeitos de conformidade.

Existem três categorias principais para fazer a auditoria de atividades relacionadas no portal do Azure:

- Utilizadores e grupos   

- Aplicações

- Diretório   


Para obter uma lista completa de atividades de relatório de auditoria, veja a [lista de eventos de relatório de auditoria](active-directory-reporting-audit-events.md#list-of-audit-report-events).


O ponto de entrada para todos os dados de auditoria é **Registos de auditoria** na secção **Atividade** do **Azure Active Directory**.


![Auditoria](./media/active-directory-reporting-azure-portal/61.png "Auditing")


Um registo de auditoria tem uma vista de lista que mostra os atores (quem), as atividades (o quê) e os destinos.


![Auditoria](./media/active-directory-reporting-azure-portal/345.png "Auditing")


Ao clicar num item na vista de lista, pode obter mais detalhes sobre o mesmo.

![Auditoria](./media/active-directory-reporting-azure-portal/873.png "Auditing")




### Registos de auditoria de utilizadores e grupos


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


![Auditoria](./media/active-directory-reporting-azure-portal/93.png "Auditing")


### Registos de auditoria de aplicação

Com os relatórios de auditoria baseados em aplicações, poderá obter respostas a perguntas como:

- Quais são as aplicações que foram adicionadas ou atualizadas?

- Quais são as aplicações que foram removidas?

- Um principal de serviço para uma aplicação foi alterado?

- Os nomes das aplicações foram alterados?

- Quem autorizou uma aplicação?


Se quiser apenas rever dados de auditoria que estejam relacionados com aplicações, pode encontrar um vista filtrada em **Registos de auditoria** na secção **Atividade** de **Aplicações empresariais**.


![Auditoria](./media/active-directory-reporting-azure-portal/134.png "Auditing")


### Filtrar registos de auditoria

Pode filtrar um relatório de auditoria por um intervalo de tempo para limitar a quantidade de dados apresentados.

![Auditoria](./media/active-directory-reporting-azure-portal/324.png "Auditing")

Outro método para filtrar as entradas de um registo de auditoria consiste em procurar entradas específicas.

![Auditoria](./media/active-directory-reporting-azure-portal/237.png "Auditing")

## Passos seguintes

Veja o [Guia dos Relatórios do Azure Active Directory](active-directory-reporting-guide.md).



<!--HONumber=Sep16_HO3-->


