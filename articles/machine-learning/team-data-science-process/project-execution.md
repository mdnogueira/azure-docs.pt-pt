---
title: "Execução de projetos de ciência de dados - Azure | Microsoft Docs"
description: "Como um scientist de dados pode executar um projeto de ciência de dados no trackable, versão controladas e a forma de colaboração."
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
ms.date: 11/16/2017
ms.author: bradsev;
ms.openlocfilehash: 1015a9f24ca2c175ff367b1748f05bb3e464457f
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="execution-of-data-science-projects"></a>Execução de projetos de ciência de dados

Este documento descreve como os programadores podem executar um projeto de ciência de dados num systematic, versão controladas e forma colaboração dentro de uma equipa de projeto, utilizando o [o processo de ciência de dados de equipa](overview.md) (TDSP). O TDSP é uma arquitetura desenvolvida pela Microsoft que fornece uma sequência de atividades para executar as soluções de Análise Preditiva baseado na nuvem, de forma eficiente structured. Para uma descrição das funções de técnicos e das respetivas tarefas associadas são processados por um dados ciência equipa uniformizar neste processo, consulte [funções do processo de ciência de dados de equipa e tarefas](roles-tasks.md). 

Este artigo inclui instruções sobre como: 

1. Efetue **sprint planeamento** para itens de trabalho envolvidos num projeto.<br> Se não estiver familiarizado com o planeamento de sprint, pode encontrar os detalhes e informações gerais [aqui](https://en.wikipedia.org/wiki/Sprint_(software_development) "aqui"). 
2. **Adicionar itens de trabalho** para sprints.
3. **ligar os itens de trabalho com atividades de codificação** controlados pelo git.
4. Efetue **code revisão**. 

> [!NOTE]
> Os passos necessários para configurar um ambiente de equipa TDSP utilizando os serviços de equipa do Visual Studio (VSTS) são descritos na seguinte conjunto de instruções. Se especificarem como realizar estas tarefas com VSTS, uma vez que é como implementar TDSP na Microsoft.  Se optar por utilizar VSTS, itens (3) e (4) na lista anterior são as vantagens que obtém naturalmente. Se for utilizado outro código de plataforma de alojamento para o grupo, as tarefas que necessitam de ser concluídos pela antecedência de equipa, geralmente, não alteram. Mas a forma para concluir estas tarefas vai ser diferentes. Por exemplo, o item na secção seis, **associar um item de trabalho com um ramo de git**, poderá não ser tão fácil porque esta está no VSTS.
>
>

A figura seguinte ilustra uma sprint típico planeamento, a codificação e fluxo de trabalho do controlo de código fonte envolvidos na implementação de um projeto de ciência de dados:

![1](./media/project-execution/project-execution-1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologia 

No TDSP sprint framework planeamento, existem quatro tipos utilizados frequentemente de **itens de trabalho**: **funcionalidade**, **história de utilizador**, **tarefas**, e **Erros**. Cada projeto de equipa mantém um registo de segurança único para todos os itens de trabalho. Não há nenhum registo de segurança ao nível do repositório de git num projeto de equipa. Seguem-se as respetivas definições:

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

Cientistas de dados podem sentir mais confortável com um modelo seja ágil especificamente alinhar com as fases do ciclo de vida TDSP. Com que em mente, um sprint derivadas Agile planear o modelo foi criado, onde Epics, etc. da Stories são substituídos por fases do ciclo de vida TDSP ou substages. Pode encontrar documentação sobre como criar um modelo seja ágil essa [aqui](https://msdata.visualstudio.com/AlgorithmsAndDataScience/TDSP/_git/TDSP?path=%2FDocs%2Fteam-data-science-process-agile-template.md&version=GBxibingao&_a=preview).


##  2. <a name='SprintPlanning-2'></a>Sprint planeamento 

Planeamento de Sprint é útil para atribuição de prioridades de projeto e o planeamento de recursos e a alocação. Muitos cientistas de dados fazem parte com vários projetos, cada um dos quais pode demorar meses para concluir. Muitas vezes, continuar projetos em paces diferentes. No servidor de VSTS, pode facilmente criar, gerir e controlar os itens de trabalho no seu projeto de equipa e realize sprint planeamento garantir que os seus projetos estiver a mover reencaminhar conforme esperado. 

Siga [esta ligação](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) para as instruções passo a passo sprint planeamento no VSTS. 


##  3. <a name='AddFeature-3'></a>Adicionar uma funcionalidade  

Depois de criado o seu repositório de projeto sob um projeto de equipa, vá para a equipa **descrição geral** página e clique em **gerir trabalho**.

![2](./media/project-execution/project-execution-2-sprint-team-overview.png)

Para incluir uma funcionalidade no registo de segurança, clique em **os registos de segurança** --> **funcionalidades** --> **novo**, tipo na funcionalidade **título**(normalmente, no nome do projeto) e, em seguida, clique em **adicionar** .

![3](./media/project-execution/project-execution-3-sprint-team-add-work.png)

Faça duplo clique a funcionalidade que criou. Preencha as descrições, atribuir membros do agrupamento para esta funcionalidade e conjunto de parâmetros para esta funcionalidade de planeamento. 

Também pode ligar esta funcionalidade para o repositório de projeto. Clique em **hiperligação Adicionar** sob o **desenvolvimento** secção. Depois de acabar de editar a funcionalidade, clique em **guardar e fechar** para sair.


##  4. <a name='AddStoryunderfeature-4'></a>Adicionar bloco sob funcionalidade 

Sob a funcionalidade stories podem ser adicionados para descrever os principais passos necessários para concluir o projeto (funcionalidade). Para adicionar um novo bloco, clique o  **+**  iniciar sessão para a esquerda da funcionalidade na vista de registo de segurança.  

![4](./media/project-execution/project-execution-4-sprint-add-story.png)

Pode editar os detalhes do bloco, tais como o estado, a descrição, comentários, planeamento e prioridade na janela de pop-up.

![5](./media/project-execution/project-execution-5-sprint-edit-story.png)

Pode ligar este bloco para um repositório existente ao clicar em **+ adicionar ligação** em **desenvolvimento**. 

![6](./media/project-execution/project-execution-6-sprint-link-existing-branch.png)


##  5. <a name='AddTaskunderstory-5'></a>Adicionar uma tarefa a uma história 

As tarefas são específicos os passos detalhados que são necessárias para concluir cada bloco. Depois de concluídas todas as tarefas de um bloco, o bloco deve ser concluído demasiado. 

Para adicionar uma tarefa para um bloco, clique o  **+**  sessão junto ao item da bloco, selecione **tarefas**e, em seguida, preencha as informações detalhadas desta tarefa na janela de pop-up.

![7](./media/project-execution/project-execution-7-sprint-add-task.png)

Depois das funcionalidades, stories e tarefas de criação, pode visualizá-las no **registo de segurança** ou **quadro** vistas para controlar o respetivo estado.

![8](./media/project-execution/project-execution-8-sprint-backlog-view.png)

![9](./media/project-execution/project-execution-9-link-to-a-new-branch.png)


##  6. <a name='Linkaworkitemwithagitbranch-6'></a>Ligar um item de trabalho com um ramo de git 

VSTS fornece uma maneira conveniente para ligar um item de trabalho (uma história ou tarefa) com um ramo de git. Isto permite-lhe associar o seu bloco ou a tarefa diretamente para o código associado. 

Para ligar um item de trabalho para um novo ramo, faça duplo clique num item de trabalho e, na janela de pop-up, clique em **criar um novo ramo** em **+ adicionar ligação**.  

![10](./media/project-execution/project-execution-10-sprint-board-view.png)

Forneça as informações para este ramo novo, tais como o nome de sucursal, repositório de base git e o ramo. O repositório de git escolhido tem de ser o repositório sob o mesmo projeto de equipa que pertence o item de trabalho. O ramo base pode ser o ramo principal ou alguns outro ramo existente.

![11](./media/project-execution/project-execution-11-create-a-branch.png)

É uma boa prática criar um ramo de git para cada item de trabalho de bloco. Em seguida, para cada item de trabalho de tarefas, crie um ramo com base no ramo do bloco. Organizar os ramos desta forma hierárquica que corresponde às relações de tarefa do bloco é útil quando tem várias pessoas trabalhar stories diferentes do mesmo projeto ou tem várias pessoas diferentes tarefas do mesmo bloco a trabalhar. Podem ser minimizados conflitos quando cada membro do agrupamento funciona num ramo diferentes e quando cada membro funciona em códigos diferentes ou outros artefactos quando partilha um ramo. 

A imagem seguinte ilustra a estratégia de ramificação recomendada para TDSP. Não poderá ser necessário como muitas ramificações que são mostradas aqui, especialmente quando tiver apenas uma ou duas pessoas trabalhar no mesmo projecto ou apenas uma pessoa funciona em todas as tarefas de um bloco. Mas separar o ramo de desenvolvimento do ramo principal é sempre boa prática. Isto pode ajudar a impedir que o ramo de versão que está a ser interrompida pelas atividades de desenvolvimento. Pode encontrar mais completa descrição do modelo de sucursal de git [A bem-sucedida Git ramificação modelo](http://nvie.com/posts/a-successful-git-branching-model/).

![12](./media/project-execution/project-execution-12-git-branches.png)

Para mudar para o ramo que pretende trabalhar, execute o comando seguinte num comando shell (Windows ou Linux). 

    git checkout <branch name>

Alterar o *< nome do ramo\>*  para **mestre** comutadores novamente para o **mestre** ramo. Depois de mudar para o ramo de trabalho, pode começar a trabalhar este item de trabalho, desenvolver os artefactos de código ou documentação necessários para concluir o item. 

Também pode ligar um item de trabalho para um ramo existente. No **detalhe** página de um item de trabalho, em vez de clicar **criar um novo ramo**, clicar em **+ adicionar ligação**. Em seguida, selecione o ramo que pretende associar o item de trabalho para. 

![13](./media/project-execution/project-execution-13-link-to-an-existing-branch.png)

Também pode criar um novo ramo no git bash comandos. Se < nome da base de sucursal\> está em falta, a < novo nome de sucursal\> baseia-se no _mestre_ ramo. 
    
    git checkout -b <new branch name> <base branch name>


##  7. <a name='WorkonaBranchandCommittheChanges-7'></a>Um ramo de trabalho e confirmar as alterações 

Agora Suponhamos que efetuar algumas alterações para o *dados\_ingestão* ramo para o item de trabalho, como adicionar um ficheiro de R no ramo no seu computador local. Pode consolidar o ficheiro de R adicionado para o ramo para este item de trabalho, desde que estejam nessa sucursal na sua shell de Git, utilizando os seguintes comandos de Git:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![14](./media/project-execution/project-execution-14-sprint-push-to-branch.png)

##  8. <a name='CreateapullrequestonVSTS-8'></a>Criar um pedido pull no VSTS 

Quando estiver pronto, após alguns consolidações e pushes, intercalar o ramo atual para o ramo base, pode submeter um **pedido pull** no servidor VSTS. 

Aceda à página principal do seu projeto de equipa e clique em **código**. Selecione o ramo ser intercaladas e o nome do repositório de git que pretende intercalar o ramo para. Em seguida, clique em **pedidos de solicitação**, clique em **novo pedido de solicitação** para criar uma revisão de pedido de solicitação antes do trabalho no ramo intercalado para o ramo base.

![15](./media/project-execution/project-execution-15-spring-create-pull-request.png)

Preencha algumas descrição sobre este pedido de solicitação, adicione os revisores e enviá-lo a.

![16](./media/project-execution/project-execution-16-spring-send-pull-request.png)

##  9. <a name='ReviewandMerge-9'></a>Reveja e intercalação 

Quando é criado o pedido de solicitação, os revisores de obter uma notificação por e-mail para rever os pedidos de solicitação. Os revisores tem de verificar se as alterações estão a funcionar ou não e testar as alterações com o autor do pedido se possível. Os revisores com base na respetiva avaliação, podem aprovar ou rejeitar o pedido de solicitação. 

![17](./media/project-execution/project-execution-17-add_comments.png)

![18](./media/project-execution/project-execution-18-spring-approve-pullrequest.png)

Depois de terminar a revisão, o ramo de trabalho intercalado para o ramo base clicando a **concluída** botão. Pode optar por eliminar o ramo de trabalho após foi intercalado. 

![19](./media/project-execution/project-execution-19-spring-complete-pullrequest.png)

Confirme no canto superior esquerdo que o pedido está marcado como **CONCLUÍDO**. 

![20](./media/project-execution/project-execution-20-spring-merge-pullrequest.png)

Quando lhe voltar atrás para o repositório em **código**, são disse que lhe foi mudada para o ramo principal.

![21](./media/project-execution/project-execution-21-spring-branch-deleted.png)

Também pode utilizar os seguintes comandos de Git para intercalar o ramo de trabalho para o ramo base e elimine o ramo de trabalho após a intercalação:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![22](./media/project-execution/project-execution-22-spring-branch-deleted-commandline.png)


##  10. <a name='DataQualityReportUtility-10'></a>Exploração de dados interativa, análise e relatórios utilitário (IDEAR)

Este utilitário de baseado no markdown de R fornece uma ferramenta flexível e interativa para avaliar e explore os conjuntos de dados. Os utilizadores podem gerar rapidamente os relatórios do conjunto de dados com codificação mínima. Os utilizadores podem clicar botões para exportar os resultados de exploração na ferramenta de interativa para um relatório final, o que pode ser distribuído aos clientes ou utilizado para tomar decisões sobre as variáveis para incluir o passo de modelação subsequentes.

Neste momento, a ferramenta só funciona em pacotes de dados na memória. É necessário um ficheiro de .yaml para especificar os parâmetros de conjunto de dados para ser explorou. Para obter mais informações, consulte [IDEAR no utilitários de ciência de dados de TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


##  11. <a name='ModelingUtility-11'></a>Modelação de linha de base e o utilitário de relatórios

Este utilitário fornece uma ferramenta personalizável, por automatizadas para efetuar a criação de modelo com hyper varrimento como parâmetro e comparar a precisão desses modelos. 

O utilitário de criação de modelo é um ficheiro de markdown de R que pode ser executado para produzir a saída de html autónomo com uma tabela de conteúdos para a navegação fácil através do respetivas diferentes secções. Três algoritmos são executados quando o ficheiro de markdown é executado (knit): regressão regularized utilizando o glmnet do pacote, aleatória floresta utilizando o pacote de randomForest e os aumentos árvores utilizando o pacote de xgboost). Cada um destes algoritmos produz um modelo preparado. É comparada com a precisão destes modelos e a importância de funcionalidade relativo rastreia é reportado. Atualmente, existem dois utilitários: uma é para uma tarefa de classificação binária e um é uma tarefa de regressão. As principais diferenças entre eles é os parâmetros de controlo de forma e métricas de precisão especificadas para estas tarefas de aprendizagem. 

Um ficheiro de Yaml é utilizado para especificar:

- a entrada de dados (uma origem SQL ou um ficheiro de dados de R) 
- que parte dos dados é utilizada para formação e que parte para fins de teste
- que algoritmos para executar 
- a escolha de parâmetros de controlo para a otimização de modelo:
    - validação cruzada 
    - bootstrapping
    - subconjuntos de validação de validação cruzada
- os conjuntos de hyper-parâmetro de cada algoritmo. 

O número de algoritmos, o número de subconjuntos de validação para otimização, os hyper-os parâmetros e o número de conjuntos de parâmetro hyper para sweep através de também podem ser modificadas no ficheiro Yaml para executar os modelos rapidamente. Por exemplo, pode ser executadas com um número inferior de subconjuntos de validação do CV, número de conjuntos de parâmetros. Estes também podem ser executadas mais abrangente com um número mais alto de subconjuntos de validação CV ou um grande número de conjuntos de parâmetros, se de que é garantido.

Para obter mais informações, consulte [automatizada modelação e relatórios utilitário no utilitários de ciência de dados de TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).


##  12. <a name='PowerBI-12'></a>Controlar o progresso dos projetos de dashboards do Power BI

Gestores de grupo de ciência de dados, clientes potenciais clientes potenciais de equipa e a necessidade de clientes potenciais clientes potenciais de projeto para acompanhar o progresso dos seus projetos, o trabalho tiver sido realizado nos mesmos e por quem e permanece na lista de ações pendentes. Se estiver a utilizar VSTS, é capaz de criar dashboards do Power BI para controlar as atividades e os itens de trabalho associados um repositório de Git. Para obter mais informações sobre como ligar o Power BI ao Visual Studio Team Services, consulte [ligue o Power BI para a equipa de serviços](https://www.visualstudio.com/en-us/docs/report/powerbi/connect-vso-pbi-vs). 

Para saber como criar dashboards do Power BI e relatórios para controlar as atividades do repositório de Git e os itens de trabalho depois dos dados de VSTS estão ligados ao Power BI, consulte [relatórios e dashboards do Power BI crie](https://www.visualstudio.com/en-us/docs/report/powerbi/report-on-vso-with-power-bi-vs). 

Seguem-se dois dashboards de exemplo simples que são criadas para controlar as atividades do Git e itens de trabalho. No dashboard do primeiro exemplo, as atividades de compromisso de git são listadas por diferentes utilizadores, datas diferentes e repositórios diferentes. Pode facilmente segmentação e decomposição para filtrar aqueles que está interessado em.

![23](./media/project-execution/project-execution-23-powerbi-git.png)

No dashboard de exemplo do segundo, são apresentados os itens de trabalho (stories e tarefas) no iterações diferentes. Estes são agrupados por assignees e níveis de prioridade e coloridos por Estado.

![24](./media/project-execution/project-execution-24-powerbi-workitem.png)

 
## <a name="next-steps"></a>Passos seguintes

Total de instruções ponto-a-ponto que demonstram todos os passos no processo de **cenários específicos** também são fornecidos. São listados e ligados com descrições de miniaturas no [instruções de exemplo](walkthroughs.md) artigo. Estes mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Para obter exemplos executar os passos no processo de ciência de dados de equipa que utilizam o Azure Machine Learning Studio, consulte o [com o Azure ML](http://aka.ms/datascienceprocess) percurso de aprendizagem.