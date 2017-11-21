---
title: "Utilizar o repositório de Git com uma projeto do Workbench de aprendizagem do Azure | Microsoft Docs"
description: "Este artigo explica como utilizar um repositório de Git em conjunto com um projeto de Workbench do Azure Machine Learning."
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: fe2a302a32f1b9ec474416704c6cb613cd384a0e
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Utilizar o repositório de Git com um projeto do Azure Machine Learning Workbench
Este documento fornece informações sobre como o Azure Machine Learning Workbench utiliza o Git para fornecer controlo de versão e certifique-se reproducibility na sua experimentação de ciência de dados. Também são fornecidas instruções sobre como associar o seu projeto com uma nuvem repositório de Git.

## <a name="introduction"></a>Introdução
Azure Workbench de aprendizagem máquina concebido com a integração de Git do zero cópias de segurança. Ao criar um novo projeto, a pasta do projeto é automaticamente "inicializado pelo Git" como um repositório de Git local (repositório). Entretanto, um segundo repositório de Git local oculto também é criado com uma sucursal com o nome _AzureMLHistory / < project_GUID >_ para controlar as alterações da pasta do projeto para cada execução. 

Associar o projeto do Azure ML um repositório de Git, alojado dentro de um projeto de serviço de equipa do Visual Studio (VSTS), permite o controlo de versão automático localmente e remotamente. Esta associação permite que qualquer pessoa com acesso ao repositório remoto transferir o código de origem mais recente para outro computador (roaming).  

> [!NOTE]
> VSTS tem a sua própria lista de controlo de acesso que seja independente do serviço de experimentação do Azure Machine Learning. Acesso de utilizador pode variar entre um repositório de Git e uma área de trabalho do Azure ML ou o projeto e poderá ter de ser geridos. Para que o se pretender partilhar o projeto do Azure ML com um membro de equipa, incluindo apenas o acesso de nível de código, para além partilha a área de trabalho, terá de conceder explicitamente him/her acesso adequado para o repositório de VSTS Git. 

Com o Git, também é possível gerir o controlo de versão explicitamente utilizando o _mestre_ Ramificação interna ou por criar outros ramos no repositório. Pode utilizar apenas o repositório de Git local e também pode enviar para o repositório de Git remoto se aprovisionado.

Este diagrama ilustra a relação entre um repositório de VSTS Git e um projeto do Azure ML:

![AML Git](media/using-git-ml-project/aml_git.png)

Para começar a utilizar um repositório de Git remoto, siga estas instruções básicas.

> [!NOTE]
> Atualmente, o Azure Machine Learning suporta apenas a repositórios de Git no VSTS contas. Suporte para Git geral repos (por exemplo, o GitHub e etc.) está a ser planeado a no futuro.

## <a name="step-1-create-an-azure-ml-experimentation-account"></a>Passo 1. Criar uma conta de experimentação do Azure ML
Se não estiver já, crie uma conta de experimentação do Azure ML e instalar a aplicação do Azure ML Workbench. Ver mais detalhes no [instalar e criar o guia de introdução](quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>Passo 2. Criar um projeto de equipa ou utilizar um projeto de equipa existente
De [portal do Azure](https://portal.azure.com/), crie um novo **projeto de equipa**.
1. Clique em**+**
2. Procurar **"Projeto de equipa"**
3. Introduza as informações necessárias.
    - Nome: Um nome de agrupamento.
    - Controlo de versão: **Git**
    - Subscrição: Uma com uma conta de experimentação do Azure Machine Learning.
    - Localização: Idealmente permanecem numa região próximo os recursos do Azure Machine Learning experimentação.
4. Clique em **Criar**. 

![Criar um projeto de equipa a partir do Portal do Azure](media/using-git-ml-project/create_vsts_team.png)

Certifique-se de que iniciar sessão com a mesma conta do Azure Active Directory (AAD) estiver a utilizar para aceder ao Workbench do Azure Machine Learning. Caso contrário, o sistema não consegue acedê-la utilizando a sua credencial do AAD, a menos que utilize a linha de comandos para criar o projeto do Azure ML e fornecer um token de acesso pessoal para aceder o repositório de Git. Obter mais informações sobre esta mais tarde.

Assim que for criado o projeto de equipa, está pronto para mudar para o passo seguinte.

Para navegar diretamente para o projeto de equipa que acabou de criar, o URL é `https://<team_project_name>.visualstudio.com`.

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>Passo 3. Criar um novo projeto do Azure ML com um repositório de Git remoto
Inicie o Workbench do Azure ML e criar um novo projeto. Preencha a caixa de texto do repositório de Git com o URL do repositório de VSTS Git que obtém do passo 2. -Normalmente, este aspeto:`http://<vsts_account_name>.visualstudio.com/_git/<project_name>`

![Criar projeto do Azure ML com o repositório de Git](media/using-git-ml-project/create_project_with_git_rep.png)

Também pode criar o projeto utilizando a ferramenta de linha de comandos. Tem a opção para fornecer um token de acesso pessoal. Azure ML pode utilizar este token para o repositório de Git em seu nome, em vez de depender a sua credencial do AAD de acesso:

```
# create a new project with a Git repo and personal access token.
$ az ml project create -a <experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <VSTS personal access token>
```
> [!IMPORTANT]
> Se escolher o modelo de projeto em branco, é OK se o repositório de Git que escolher já tiver um _mestre_ ramo. Azure ML clones simplesmente o _mestre_ sucursal localmente e adicione o `aml_config` pasta e outros ficheiros de metadados para a pasta do projeto local do projeto. Mas se optar por qualquer outro modelo de projeto, o repositório de Git não já tem de ter um _mestre_ ramo ou será apresentado um erro. A alternativa consiste em utilizar `az ml project create` ferramenta de linha de comandos para criar o projeto e fornecer um `--force` mudar. Isto elimina os ficheiros no ramo principal original e substitua-as com os novos ficheiros no modelo que escolher.

Agora é criado um novo projeto do Azure ML com a integração do repositório de Git remota ativado e pronto para começar. A pasta do projeto é sempre inicializado pelo Git como um repositório de Git local. E o Git _remoto_ está definido para o repositório de VSTS Git remoto para consolidações podem ser enviadas para o repositório de Git remoto.

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>Passo 3a. Associar um projeto existente do Azure ML com um repositório de VSTS Git
Opcionalmente, pode também criar um projeto do Azure ML sem um repositório de VSTS Git e apenas pode confiar no repositório de Git local para instantâneos do histórico de execução. E pode associar um repositório de VSTS Git mais tarde este projeto do Azure ML existente utilizando o seguinte comando:

```azurecli
# make sure you are in the project path so CLI has context of your current project
$ az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name>
```

> [!NOTE] 
> Só pode efetuar a operação de repositório de atualização de um projeto do Azure ML que não tenha um repositório de Git associado à mesma. E, depois do repositório de Git está associado, não pode ser removido.

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>Passo 4. Captura de instantâneos de projeto no repositório de Git
Agora pode executar alguns script é executado no projeto, certifique-algumas alterações entre o é executado. Pode fazê-a aplicação de ambiente de trabalho, ou do CLI utilizando `az ml experiment submit` comando. Para obter mais detalhes, pode seguir o [tutorial classificar Iris](tutorial-classifying-iris-part-1.md). Para cada execução, se existir qualquer alteração efetuada em todos os ficheiros na pasta do projeto, um instantâneo da pasta do projeto todo está consolidado e enviado para o repositório de Git remoto sob um ramo com o nome `AzureMLHistory/<Project_GUID>`. Pode ver os ramos e consolidações ao navegar para o URL do repositório de VSTS Git e localizar este ramo. 

> [!NOTE] 
> O instantâneo só será aprovado antes da execução do script. Atualmente uma preparação de dados ou uma execução de célula do bloco de notas da execução não aciona o instantâneo.

![ramo de histórico de execução](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Recomenda-se de que não funcionam no ramo histórico utilizando comandos de Git. Se o fizer, poderá mess segurança histórico de execução. Utilizar o ramo principal ou criar outros ramos em vez disso, para as seus próprios operações de Git.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Passo 5. Restaurar um instantâneo anterior do projeto 
Para restaurar a pasta do projeto todo para o estado de um instantâneo estado de projeto da histórico de execução anterior, a partir do Azure ML Workbench:
1. Clique em **executa** na atividade de barra (ícone glass hora).
2. Do **executar lista** ver, clique em execução que pretende restaurar.
3. Do **executar detalhe** ver, clique em **restaurar**.

![ramo de histórico de execução](media/using-git-ml-project/restore_project.png)

Em alternativa, pode utilizar o seguinte comando na janela da CLI do Azure ML Workbench.

```azurecli
# discover the run I want to restore snapshot from:
$ az ml history list -o table

# restore the snapshot from a particular run
$ az ml project restore --run-id <run_id>
```

Ao executar este comando, iremos irá substituir a pasta do projeto todo com o instantâneo tirado quando esse específico run foi arrancou. Mas o seu projeto situa-se no ramo atual. Isto significa que poderá **perder todas as alterações** na pasta do projeto atual. Por isso, tenha muito cuidado quando executar este comando. Pode querer Git para consolidar as alterações para o ramo atual antes de efetuar a operação acima. Consulte antes para obter mais detalhes.

## <a name="step-6-use-the-master-branch"></a>Passo 6. Utilize o ramo principal
Uma forma de evitar acidentalmente perder o seu estado atual do projeto, é consolidar o projeto para o ramo principal (ou qualquer ramo criado por si) do repositório de Git. Pode utilizar diretamente Git de linha de comandos (ou a outra favorita Git ferramenta de cliente da preferência) a funcionar o ramo principal. Por exemplo:

```
# check status to make sure you are on the master branch (or branch of your choice)
$ git status

# stage all changes
$ git add -A

# commit all changes locally on the master branch
$ git commit -m 'these are my updates so far'

# push changes into the remote VSTS Git repo master branch.
$ git push origin master
```

Agora, pode restaurar em segurança projeto para um instantâneo anterior seguinte passo 5, sabendo que, pode sempre voltar para a consolidação apenas efetuadas no principal ramo.

## <a name="authentication"></a>Autenticação
Se confiar apenas as funções de histórico de execução do Azure ML para tirar instantâneos do projeto e o restauro, não precisa de preocupar com a autenticação do repositório de Git. É tratado pela camada de serviço de experimentação.

No entanto, se utilizar as suas próprias ferramentas de Git para gerir o controlo de versão, terá de processar corretamente a autenticação contra o repositório de Git remoto no VSTS. No Azure ML, o repositório de Git remoto é adicionado ao repositório local como um Git remoto através do protocolo HTTPS. Isto significa quando emitir Git os comandos remotamente (tais como push ou pull), que tem de fornecer o nome de utilizador e palavra-passe ou token de acesso pessoal. Siga [estas instruções](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) para criar o token de acesso pessoal um repositório de VSTS Git.

## <a name="next-steps"></a>Passos seguintes
Saiba como utilizar o processo de ciência de dados de agrupamento para organizar a estrutura do projeto, consulte [estruturar um projeto com TDSP](how-to-use-tdsp-in-azure-ml.md)
