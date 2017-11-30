---
title: "Desenvolvimento seja ágil de projetos de ciência de dados - Azure Machine Learning | Microsoft Docs"
description: "Como os programadores podem executar um projeto de ciência de dados numa versão systematic, controlada e forma colaboração dentro de uma equipa de projeto, utilizando o processo de ciência de dados de equipa."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: 686f751b241d49d116948711c683f4b504d5d5f9
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="agile-development-of-data-science-projects"></a>Desenvolvimento seja ágil de projetos de ciência de dados

Este documento descreve como os programadores podem executar um projeto de ciência de dados num systematic, versão controladas e forma colaboração dentro de uma equipa de projeto, utilizando o [o processo de ciência de dados de equipa](overview.md) (TDSP). O TDSP é uma arquitetura desenvolvida pela Microsoft que fornece uma sequência de atividades para executar as soluções de Análise Preditiva baseado na nuvem, de forma eficiente structured. Para uma descrição das funções de técnicos e das respetivas tarefas associadas são processados por um dados ciência equipa uniformizar neste processo, consulte [funções do processo de ciência de dados de equipa e tarefas](roles-tasks.md). 

Este artigo inclui instruções sobre como: 

1. Efetue **sprint planeamento** para itens de trabalho envolvidos num projeto.<br> Se não estiver familiarizado com o planeamento de sprint, pode encontrar os detalhes e informações gerais [aqui](https://en.wikipedia.org/wiki/Sprint_(software_development) "aqui"). 
2. **Adicionar itens de trabalho** para sprints. 

> [!NOTE]
> Os passos necessários para configurar um ambiente de equipa TDSP utilizando os serviços de equipa do Visual Studio (VSTS) são descritos na seguinte conjunto de instruções. Se especificarem como realizar estas tarefas com VSTS, uma vez que é como implementar TDSP na Microsoft.  Se optar por utilizar VSTS, itens (3) e (4) na lista anterior são as vantagens que obtém naturalmente. Se for utilizado outro código de plataforma de alojamento para o grupo, as tarefas que necessitam de ser concluídos pela antecedência de equipa, geralmente, não alteram. Mas a forma para concluir estas tarefas vai ser diferentes. Por exemplo, o item na secção seis, **associar um item de trabalho com um ramo de Git**, poderá não ser tão fácil porque esta está no VSTS.
>
>

A figura seguinte ilustra uma sprint típico planeamento, a codificação e fluxo de trabalho do controlo de código fonte envolvidos na implementação de um projeto de ciência de dados:

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologia 

No TDSP sprint framework planeamento, existem quatro tipos utilizados frequentemente de **itens de trabalho**: **funcionalidade**, **história de utilizador**, **tarefas**, e **Erros**. Cada projeto de equipa mantém um registo de segurança único para todos os itens de trabalho. Não há nenhum registo de segurança ao nível do repositório de Git num projeto de equipa. Seguem-se as respetivas definições:

- **Funcionalidade**: uma funcionalidade corresponde a um envolvimento de projeto. Ações de envolvimento diferentes com um cliente são consideradas funcionalidades diferentes. Da mesma forma, é melhor a ter em consideração diferentes fases de um projeto com um cliente como funcionalidades diferentes. Se escolher um esquema, tal como ***ClientName EngagementName*** para nomear as funcionalidades, em seguida, pode facilmente reconhecer o contexto do projeto de envolvimento dos nomes próprios.
- **Bloco**: Stories são itens de trabalho diferente que são necessárias para concluir uma funcionalidade (projeto) ponto-a-ponto. Os exemplos de stories incluem:
    - Obter dados 
    - Explorar os dados 
    - Gerar funcionalidades
    - Criação de modelos
    - Operacionalizar modelos 
    - Reparametrização modelos
- **Tarefa**: as tarefas são itens de trabalho de código ou documento pode ser atribuídos ou outras atividades que têm de ser efetuadas para concluir uma história específica. Por exemplo, as tarefas no bloco *obter dados* pode ser:
    -  Obter credenciais do SQL Server 
    -  Carregar dados do armazém de dados do SQL Server. 
- **Erros**: erros referir-se normalmente a correções que são necessários para um código existente ou o documento que são efetuadas quando concluir uma tarefa. Se o erro é causado por falta fases ou tarefas, respetivamente, este pode escalar a ser uma história ou uma tarefa. 

> [!NOTE]
> Conceitos são borrowed de funcionalidades, stories, tarefas e erros da gestão de código de software (SCM) para ser utilizado em ciência de dados. Poderão diferir ligeiramente das respetivas definições SCM convencionais.
>
>

> [!NOTE]
> Cientistas de dados podem sentir mais confortável com um modelo seja ágil especificamente alinhar com as fases do ciclo de vida TDSP. Com que em mente, um sprint derivadas Agile planear o modelo foi criado, onde Epics, etc. da Stories são substituídos por fases do ciclo de vida TDSP ou substages. Para obter instruções sobre como criar um modelo seja ágil, consulte [configurar o processo de ciência de dados seja ágil no Visual Studio Online](agile-development.md#set-up-agile-dsp-6).
>
>

## 2. <a name='SprintPlanning-2'></a>Sprint planeamento 

Planeamento de Sprint é útil para atribuição de prioridades de projeto e o planeamento de recursos e a alocação. Muitos cientistas de dados fazem parte com vários projetos, cada um dos quais pode demorar meses para concluir. Muitas vezes, continuar projetos em paces diferentes. No servidor de VSTS, pode facilmente criar, gerir e controlar os itens de trabalho no seu projeto de equipa e realize sprint planeamento garantir que os seus projetos estiver a mover reencaminhar conforme esperado. 

Siga [esta ligação](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) para as instruções passo a passo sprint planeamento no VSTS. 


## 3. <a name='AddFeature-3'></a>Adicionar uma funcionalidade  

Depois de criado o seu repositório de projeto sob um projeto de equipa, vá para a equipa **descrição geral** página e clique em **gerir trabalho**.

![2](./media/agile-development/2-sprint-team-overview.png)

Para incluir uma funcionalidade no registo de segurança, clique em **os registos de segurança** --> **funcionalidades** --> **novo**, tipo na funcionalidade **título**(normalmente, no nome do projeto) e, em seguida, clique em **adicionar** .

![3](./media/agile-development/3-sprint-team-add-work.png)

Faça duplo clique a funcionalidade que criou. Preencha as descrições, atribuir membros do agrupamento para esta funcionalidade e conjunto de parâmetros para esta funcionalidade de planeamento. 

Também pode ligar esta funcionalidade para o repositório de projeto. Clique em **hiperligação Adicionar** sob o **desenvolvimento** secção. Depois de acabar de editar a funcionalidade, clique em **guardar e fechar** para sair.


## 4. <a name='AddStoryunderfeature-4'></a>Adicionar bloco sob funcionalidade 

Sob a funcionalidade stories podem ser adicionados para descrever os principais passos necessários para concluir o projeto (funcionalidade). Para adicionar um novo bloco, clique o  **+**  iniciar sessão para a esquerda da funcionalidade na vista de registo de segurança.  

![4](./media/agile-development/4-sprint-add-story.png)

Pode editar os detalhes do bloco, tais como o estado, a descrição, comentários, planeamento e prioridade na janela de pop-up.

![5](./media/agile-development/5-sprint-edit-story.png)

Pode ligar este bloco para um repositório existente ao clicar em **+ adicionar ligação** em **desenvolvimento**. 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>Adicionar uma tarefa a uma história 

As tarefas são específicos os passos detalhados que são necessárias para concluir cada bloco. Depois de concluídas todas as tarefas de um bloco, o bloco deve ser concluído demasiado. 

Para adicionar uma tarefa para um bloco, clique o  **+**  sessão junto ao item da bloco, selecione **tarefas**e, em seguida, preencha as informações detalhadas desta tarefa na janela de pop-up.

![7](./media/agile-development/7-sprint-add-task.png)

Depois das funcionalidades, stories e tarefas de criação, pode visualizá-las no **registo de segurança** ou **quadro** vistas para controlar o respetivo estado.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a>Configurar um modelo de trabalho seja ágil TDSP no Visual Studio Online

Este artigo explica como configurar um modelo de processo de ciência de dados seja ágil que utiliza as fases de ciclo de vida de ciência de dados TDSP e controla a itens de trabalho com o Visual Studio Online (vso). Os passos abaixo percurso através de um exemplo de como configurar os dados ciência específicos seja ágil processam modelo *AgileDataScienceProcess* e mostram como criar itens de trabalho de ciências de dados com base no modelo.

### <a name="agile-data-science-process-template-setup"></a>Configuração de modelo de processo de ciência de dados seja ágil

1. Navegue para a home page do servidor, **configurar** -> **processo**.

    ![10](./media/agile-development/10-settings.png) 

2. Navegue para **todos os processos** -> **processos**, em **Agile** e clique em **criar herdadas processo**. Em seguida, coloque o nome de processo "AgileDataScienceProcess" e clique em **criar processo**.

    ![11](./media/agile-development/11-agileds.png)

3. Sob o **AgileDataScienceProcess** -> **tipos de item de trabalho** separador, desativar **Epic**, **funcionalidade**,  **Bloco de utilizador**, e **tarefas** tipos do item de trabalho **configurar -> desativar**

    ![12](./media/agile-development/12-disable.png)

4. Navegue para **AgileDataScienceProcess** -> **níveis de registo de segurança** separador. Mudar o nome "Epics" a "TDSP projetos" ao clicar no **configurar** -> **/mudança de nome de edição**. Na caixa de diálogo mesmo, clique em **+ novo tipo de item de trabalho** "Projeto de ciência de dados" e defina o valor de **tipo de item de trabalho predefinido** para "TDSP projeto" 

    ![13](./media/agile-development/13-rename.png)  

5. Da mesma forma, altere o nome do registo de segurança "Funcionalidades" para "TDSP fases" e adicione o seguinte para o **tipo de item de trabalho nova**:

    - Compreensão Empresarial
    - Aquisição de dados
    - Modelação
    - Implementação

6. Mudar o nome "Utilizador história" para "TDSP Substages" com o tipo de item de trabalho predefinido definido como tipo de recém-criado "TDSP Substage".

7. Definir as "tarefas" recentemente criado o tipo de item de trabalho "TDSP Task" 

8. Após estes passos, os níveis de registo de segurança devem ter o seguinte aspeto:

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>Criar itens de trabalho de ciências de dados

Depois de criado o modelo de processo de ciência de dados, pode criar e controlar os itens de trabalho de ciência de dados que correspondem ao ciclo de vida TDSP.

1. Quando cria um novo projeto de equipa, selecione "Agile\AgileDataScienceProcess" como o **processo do item de trabalho**:

    ![15](./media/agile-development/15-newproject.png)

2. Navegue para o projeto de equipa recém-criado e clique em **trabalho** -> **os registos de segurança**.

3. Tornar "TDSP projetos" visível ao clicar no **configurar as definições team** e verifique "TDSP projetos"; em seguida, guardar.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. Agora pode começar a criar os itens de trabalho específicas de ciência de dados.

    ![17](./media/agile-development/17-dsworkitems.png)

5. Eis um exemplo de como os itens de trabalho de projeto de ciência de dados devem ser apresentado:

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>Passos seguintes

[A codificação colaboração com o Git](collaborative-coding-with-git.md) descreve como fazer o desenvolvimento de colaboração código para projetos de ciência de dados utilizando o Git como o framework de desenvolvimento de código partilhado e como ligar estas atividades para o trabalho planeadas com o processo de seja ágil de codificação.

Seguem-se ligações adicionais para recursos em processos de seja ágil.

- Processo seja ágil [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Fluxo de trabalho e tipos de item de trabalho do processo seja ágil [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


Instruções que demonstram todos os passos no processo de **cenários específicos** também são fornecidos. São listados e ligados com descrições de miniaturas no [instruções de exemplo](walkthroughs.md) artigo. Estes mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 
