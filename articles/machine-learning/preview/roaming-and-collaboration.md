---
title: "Roaming e de colaboração no Azure Machine Learning Workbench | Microsoft Docs"
description: Lista dos problemas conhecidos e um guia para ajudar a resolver problemas
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/05/2017
ms.openlocfilehash: 156dd1b7f928df22b3feb9e7a13396d3b53a91d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Roaming e de colaboração no Azure Machine Learning Workbench
Este documento explica como como o Azure Machine Learning Workbench pode ajudar a percorrer o seus projetos máquinas, bem como ativar colaboração com seu teammates. 

Quando cria um projeto do Azure Machine Learning com uma ligação do repositório de Git (repositório) remota, os metadados de projeto e instantâneos são armazenados na nuvem. A ligação em nuvem permite-lhe aceda ao projeto a partir de outro computador (Roaming). Também é pode conceder acesso aos seus colegas, permitindo colaboração. 

## <a name="prerequisites"></a>Pré-requisitos
Em primeiro lugar, instale o Workbench do Azure Machine Learning com acesso a uma conta de experimentação. Siga o [guia de instalação](quickstart-installation.md) para obter mais detalhes.

Segundo, aceder [Visual Studio Team System](https://www.visualstudio.com) e criar um repositório para ligar o seu projeto para. Para obter informações detalhadas sobre o Git, referenciar a [utilizando o repositório de Git com um projeto de Workbench do Azure Machine Learning](using-git-ml-project.md) artigo.

## <a name="create-a-new-azure-machine-learning-project"></a>Criar um novo projeto do Azure Machine Learning
Inicie o Azure Machine Learning Workbench e criar um novo projeto (por exemplo, _iris_). Preencher o **URL do repositório do GIT Visualstudio.com** caixa de texto com um URL do repositório de VSTS Git válido. 
>[!IMPORTANT]
>Criação do projeto falha se não tiver acesso de leitura/escrita no repositório de Git e o repositório de Git não está vazio, ou seja, já tem um ramo principal.

Depois do projeto ser criado, submeta alguns é executado em quaisquer scripts no projeto. Esta ação consolida estado do projeto no ramo de histórico de execução do repositório de Git remoto. 

Se tiver a autenticação de Git do programa de configuração, pode também explicitamente operar no ramo principal, ou criar um novo ramo. 

Por exemplo: 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>Está em roaming
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>Abrir o Workbench do Azure Machine Learning no segundo computador
Depois do repositório de VSTS Git está ligado com o seu projeto, pode aceder a _iris_ projeto a partir de qualquer computador onde instalou o Workbench do Azure Machine Learning. 

Para aceder ao projeto iris noutro computador, terá de iniciar sessão na aplicação com as mesmas credenciais utilizadas ao criar o projeto. Além disso, terá de navegar para a mesma conta de experimentação e área de trabalho. O _iris_ projeto por ordem alfabética está listado com outros projetos dentro da área de trabalho. 

### <a name="download-project-on-second-machine"></a>Transferir o projeto na segunda máquina
Quando a área de trabalho é aberta no segundo computador, o ícone adjacente para o _iris_ projeto é diferente do ícone de pasta típica. O ícone de transferência indica que o conteúdo do projeto está na nuvem e de que tem de ser transferidos para a máquina atual. 

![Criar projeto](./media/roaming-and-collaboration/downloadable-project.png)

Clicar no _iris_ projeto inicia uma ação de transferência. Após uns breves instantes, quando a transferência ter concluído, o projeto está pronto para ser acedidas num segundo computador. 

No Windows, é`C:\Users\<username>\Documents\AzureML`

No macOS, é aqui:`/home/<username>/Documents/AzureML`

Numa versão futura, planeamos que melhoram a funcionalidade para que possa selecionar uma pasta de destino. 

>Tenha em atenção de que o se acontecer tem uma pasta no diretório do Azure ML com o mesmo nome exato como o projeto, a falha de transferência. Durante o período de tempo a ser, terá de mudar o nome de pasta existente para solucionar este problema.


### <a name="work-on-the-downloaded-project"></a>Funcionam em projeto transferido 
O projeto recentemente transferido reflete o estado de projeto a partir da última execução no projeto. Um instantâneo do Estado de projeto é automaticamente consolidado para o ramo do histórico de execução no repositório de VSTS Git sempre que submete uma execução. Utilizamos o instantâneo associado com a execução mais recente ao instanciar o projeto no segundo computador. 
 

## <a name="collaboration"></a>Colaboração
Podem colaborar com a sua teammates em projetos de um repositório de VSTS Git. Pode atribuir permissões a utilizadores na conta de experimentação, área e projeto. Neste momento, pode executar os comandos do Azure Resource Manager utilizando a CLI do Azure. Também pode utilizar [portal do Azure](https://portal.azure.com). Consulte [seguinte secção](#portal).    

### <a name="using-command-line-to-add-users"></a>Utilizando a linha de comandos para adicionar utilizadores
Permite utilizar um exemplo. Supor que, Alice é o proprietário do projeto de e_Iris_ ésimo e ela pretende partilhar o access com Bernardo. 

Alice clica no **ficheiro** menu e seleciona a **linha de comandos** item de menu para iniciar a linha de comandos, configurada para o _iris_ projeto. Alice, em seguida, é capaz de decidir que acesso fo nível que pretende conceder ao João executando os seguintes comandos.  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Reader.
# Bob now has read access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Reader --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as a Contributor.
# Bob now has read/write access to all projects under the Workspace by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <workspace ARM ID>

# find ARM ID of the project 
az ml project show --query "id"

# Add Bob to the Project as an Owner.
# Bob now has read/write access to the Project, and can add others too.
az role assignment create --assignee bob@contoso.com --role Owner --scope <project ARM ID>
```

Após a atribuição de função, diretamente ou através de herança, João pode ver o projeto na lista de projeto Workbench. A aplicação poderá necessitar de um reinício para ver o projeto. Bernardo, em seguida, pode transferir o projeto conforme descrito no [Roaming secção](#roaming) e colaborar com a Alice. 

O histórico de execução de todos os utilizadores colaborar num projeto é submetido para o mesmo repositório de Git remoto. Por isso, quando Alice executa uma execução, João pode ver a executar na secção de histórico de execução do projeto na aplicação do Workbench. João também pode restaurar o projeto para o estado de qualquer executar, incluindo executa iniciada por Alice. 

Partilhar um repositório de Git remoto para o projeto permite Alice e Bernardo também podem colaborar no ramo principal. Se for necessário, também pode criar ramos pessoais e utilizar pedidos de solicitação do Git e intercala para colaborar. 

### <a name="using-azure-portal-to-add-users"></a>Utilizar o portal do Azure para adicionar utilizadores
<a name="portal"></a>

As contas de experimentação do Machine Learning, áreas de trabalho e projetos do Azure são recursos do Azure Resource Manager. Pode utilizar a ligação de controlo de acesso no [portal do Azure](https://portal.azure.com) para atribuir funções. 

Localize o recurso que procura para adicionar utilizadores a partir de todos os recursos ver. Clique no controlo de acesso em ligação de (páginas IAM) na página. Adicionar utilizadores 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

