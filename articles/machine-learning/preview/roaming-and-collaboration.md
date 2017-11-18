---
title: "Roaming e de colaboração no Azure Machine Learning Workbench | Microsoft Docs"
description: Lista dos problemas conhecidos e um guia para ajudar a resolver problemas
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 856348c07a198a8c53c6661441d5c49196ef3af5
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
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

# Add Bob to the Experimentation Account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the Workspace by inheritance. And he can invite or remove others.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace ARM ID>
```

Após a atribuição de função, diretamente ou através de herança, João pode ver o projeto na lista de projeto Workbench. A aplicação poderá necessitar de um reinício para ver o projeto. Bernardo, em seguida, pode transferir o projeto conforme descrito no [Roaming secção](#roaming) e colaborar com a Alice. 

O histórico de execução de todos os utilizadores colaborar num projeto é submetido para o mesmo repositório de Git remoto. Por isso, quando Alice executa uma execução, João pode ver a executar na secção de histórico de execução do projeto na aplicação do Workbench. João também pode restaurar o projeto para o estado de qualquer executar, incluindo executa iniciada por Alice. 

Partilhar um repositório de Git remoto para o projeto permite Alice e Bernardo também podem colaborar no ramo principal. Se for necessário, também pode criar ramos pessoais e utilizar pedidos de solicitação do Git e intercala para colaborar. 

### <a name="using-azure-portal-to-add-users"></a>Utilizar o portal do Azure para adicionar utilizadores
<a name="portal"></a>

As contas de experimentação do Machine Learning, áreas de trabalho e projetos do Azure são recursos do Azure Resource Manager. Pode utilizar a ligação de controlo de acesso no [portal do Azure](https://portal.azure.com) para atribuir funções. 

Localize o recurso que procura para adicionar utilizadores a partir de todos os recursos ver. Clique no controlo de acesso em ligação de (páginas IAM) na página. Adicionar utilizadores 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Fluxo de trabalho de colaboração de exemplo
Para ilustrar o fluxo de colaboração, vamos guiá-lo através de um exemplo. Os funcionários do contoso Alice e João pretendem colaborar num projeto de ciência de dados através do Azure ML Workbench. A identidade de pertencer ao mesmo inquilino Contoso do Azure AD.

1. Alice cria pela primeira vez um repositório de Git vazio num projeto VSTS. Este projeto VSTS deve estar numa subscrição do Azure criada no inquilino do Contoso AAD. 

2. Alice, em seguida, cria uma conta de experimentação do Azure ML, uma área de trabalho e um projeto do Azure ML Workbench no respetivo computador. A Joana fornece o URL do repositório de Git ao criar o projeto.

3. Alice começa a trabalhar no projeto. A Joana cria alguns scripts e executa alguns é executado. Para cada execução, um instantâneo da pasta do projeto todo é automaticamente instalado para um ramo de histórico de execução do repositório de VSTS Git criado pelo Workbench como uma confirmação.

4. Alice é agora satisfeita com o trabalho em curso. Que pretende consolidar a alteração no local _mestre_ sucursal e envia-o para o repositório de VSTS Git _mestre_ ramo. Para tal, com o projeto open, a Joana inicia a janela de linha de comandos do Azure ML Workbench e, emite os seguintes comandos:
    
    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # stage all changes
    $ git add -A

    # commit changes with a comment
    $ git commit -m "this is a good milestone"

    # push the commit to the master branch of the remote Git repo in VSTS
    $ git push
    ```

5. Alice, em seguida, adiciona o Artur à área de trabalho como um contribuinte. Ela pode fazer isto do portal do Azure ou utilizar o `az role assignment` ilustrar de comando acima. Ela também concede Bernardo leitura/escrita acesso para o repositório de VSTS Git.

6. Bernardo agora registos no Azure ML Workbench no seu computador. Ele pode ver a Alice da área de trabalho partilhado com ele e o projeto listados na área de trabalho. 

7. Bernardo clica no nome do projeto e o projeto é transferido para o seu computador.
    
    a. Os ficheiros do projeto transferido são clones do instantâneo da versão mais recente executados gravados no histórico de execução. Não estão a consolidação último no ramo principal.
    
    b. A pasta local do projeto está definida no _mestre_ ramo unstaged alterações.

8. João pode procurar executa executada pela Inês e restauro de instantâneo de qualquer executa anterior.

9. João pretende obter as últimas alterações pelo Alice é feito o Push e começar a trabalhar um ramo diferentes. Por conseguinte, ele abre a janela de linha de comandos do Azure ML Workbench e executa os seguintes comandos:

    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # get the latest commit in VSTS Git master branch and overwrite current files
    $ git pull --force

    # create a new local branch named "bob" so Bob's work is done on the "bob" branch
    $ git checkout -b bob
    ```

10. Bernardo agora modifica o projeto e submeter execuções de novo. As alterações são efetuadas no _Bernardo_ ramo. E execuções do de Bob ficam visíveis para Alice bem.

11. João está agora pronto para enviar as alterações para o repositório de Git remoto. Para evitar conflitos com _mestre_ sucursal onde está a funcionar Alice, ele decide enviar o seu trabalho para um novo ramo remoto, também denominado _Bernardo_.

    ```sh
    # verify that the current branch is "bob" and it has unstaged changes
    $ git status
    
    # stage all changes
    $ git add -A

    # commit them with a comment
    $ git commit -m "I found a cool new trick."

    # create a new branch on the remote VSTS Git repo, and push changes
    $ git push origin bob
    ```

12. Bernardo, em seguida, pode dizer Alice sobre a nova truque útil no seu código e cria um pedido pull no repositório de Git remoto do _Bernardo_ sucursal para o _mestre_ ramo. E Alice, em seguida, pode intercalar o pedido de solicitação para _mestre_ ramo.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como utilizar o Git com Azure ML Workbench: [repositório de Git utilizando com um projeto do Azure Machine Learning Workbench](using-git-ml-project.md)