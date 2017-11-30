---
title: "A codificação colaboração com o Git - Azure Machine Learning | Microsoft Docs"
description: "Como fazê-lo desenvolvimento de colaboração código para projetos de ciência de dados utilizando o Git com um planeamento seja ágil."
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
ms.openlocfilehash: e6808c0d9d4ca14101eeebe8b3e286d64e2328f9
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="collaborative-coding-with-git"></a>Colaboração de programação com o Git

Neste artigo, vamos descrevem como efetuar desenvolvimento de colaboração código para projetos de ciência de dados utilizando o Git como o framework de desenvolvimento de código partilhado. Aborda como associar estas codificação atividades para o trabalho planeada no [desenvolvimento seja ágil](agile-development.md) e como codificar revisões.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>Ligar um item de trabalho com um ramo de Git 

VSTS fornece uma maneira conveniente para ligar um item de trabalho (uma história ou tarefa) com um ramo de Git. Isto permite-lhe associar o seu bloco ou a tarefa diretamente para o código associado. 

Para ligar um item de trabalho para um novo ramo, faça duplo clique num item de trabalho e, na janela de pop-up, clique em **criar um novo ramo** em **+ adicionar ligação**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Forneça as informações para este ramo novo, como o nome de sucursal, o repositório de Git base e o ramo. O repositório de Git escolhido tem de ser o repositório sob o mesmo projeto de equipa que pertence o item de trabalho. O ramo base pode ser o ramo principal ou alguns outro ramo existente.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

É uma boa prática criar um ramo de Git para cada item de trabalho de bloco. Em seguida, para cada item de trabalho de tarefas, crie um ramo com base no ramo do bloco. Organizar os ramos desta forma hierárquica que corresponde às relações de tarefa do bloco é útil quando tem várias pessoas trabalhar stories diferentes do mesmo projeto ou tem várias pessoas diferentes tarefas do mesmo bloco a trabalhar. Podem ser minimizados conflitos quando cada membro do agrupamento funciona num ramo diferentes e quando cada membro funciona em códigos diferentes ou outros artefactos quando partilha um ramo. 

A imagem seguinte ilustra a estratégia de ramificação recomendada para TDSP. Não poderá ser necessário como muitas ramificações que são mostradas aqui, especialmente quando tiver apenas uma ou duas pessoas trabalhar no mesmo projecto ou apenas uma pessoa funciona em todas as tarefas de um bloco. Mas separar o ramo de desenvolvimento do ramo principal é sempre boa prática. Isto pode ajudar a impedir que o ramo de versão que está a ser interrompida pelas atividades de desenvolvimento. Pode encontrar mais completa descrição do modelo de sucursal de Git [A bem-sucedida Git ramificação modelo](http://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Para mudar para o ramo que pretende trabalhar, execute o comando seguinte num comando shell (Windows ou Linux). 

    git checkout <branch name>

Alterar o *< nome do ramo\>*  para **mestre** comutadores novamente para o **mestre** ramo. Depois de mudar para o ramo de trabalho, pode começar a trabalhar este item de trabalho, desenvolver os artefactos de código ou documentação necessários para concluir o item. 

Também pode ligar um item de trabalho para um ramo existente. No **detalhe** página de um item de trabalho, em vez de clicar **criar um novo ramo**, clicar em **+ adicionar ligação**. Em seguida, selecione o ramo que pretende associar o item de trabalho para. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

Também pode criar um novo ramo no Git Bash comandos. Se < nome da base de sucursal\> está em falta, a < novo nome de sucursal\> baseia-se no _mestre_ ramo. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>Um ramo de trabalho e confirmar as alterações 

Agora Suponhamos que efetuar algumas alterações para o *dados\_ingestão* ramo para o item de trabalho, como adicionar um ficheiro de R no ramo no seu computador local. Pode consolidar o ficheiro de R adicionado para o ramo para este item de trabalho, desde que estejam nessa sucursal na sua shell de Git, utilizando os seguintes comandos de Git:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>Criar um pedido pull no VSTS 

Quando estiver pronto, após alguns consolidações e pushes, intercalar o ramo atual para o ramo base, pode submeter um **pedido pull** no servidor VSTS. 

Aceda à página principal do seu projeto de equipa e clique em **código**. Selecione o ramo ser intercaladas e o nome do repositório de Git que pretende intercalar o ramo para. Em seguida, clique em **pedidos de solicitação**, clique em **novo pedido de solicitação** para criar uma revisão de pedido de solicitação antes do trabalho no ramo intercalado para o ramo base.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Preencha algumas descrição sobre este pedido de solicitação, adicione os revisores e enviá-lo a.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>Reveja e intercalação 

Quando é criado o pedido de solicitação, os revisores de obter uma notificação por e-mail para rever os pedidos de solicitação. Os revisores tem de verificar se as alterações estão a funcionar ou não e testar as alterações com o autor do pedido se possível. Os revisores com base na respetiva avaliação, podem aprovar ou rejeitar o pedido de solicitação. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

Depois de terminar a revisão, o ramo de trabalho intercalado para o ramo base clicando a **concluída** botão. Pode optar por eliminar o ramo de trabalho após foi intercalado. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Confirme no canto superior esquerdo que o pedido está marcado como **CONCLUÍDO**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Quando lhe voltar atrás para o repositório em **código**, são disse que lhe foi mudada para o ramo principal.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Também pode utilizar os seguintes comandos de Git para intercalar o ramo de trabalho para o ramo base e elimine o ramo de trabalho após a intercalação:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>Passos seguintes

[Executar tarefas de ciência de dados](execute-data-science-tasks.md) mostra como utilizar utilitários para concluir a várias tarefas de ciência de dados comuns, tais como a exploração de dados interativa, análise de dados, relatórios e criação de modelo.

Instruções que demonstram todos os passos no processo de **cenários específicos** também são fornecidos. São listados e ligados com descrições de miniaturas no [instruções de exemplo](walkthroughs.md) artigo. Estes mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

