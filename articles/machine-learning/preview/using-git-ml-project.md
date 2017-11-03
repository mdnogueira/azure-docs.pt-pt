---
title: "Utilizar o repositório de Git com uma projeto do Workbench de aprendizagem do Azure | Microsoft Docs"
description: "Este artigo explica como utilizar um repositório de Git em conjunto com um projeto de Workbench do Azure Machine Learning."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: hning86
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 59b07c9834904e01256b75344ba2e6892e56438c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Utilizar o repositório de Git com um projeto do Azure Machine Learning Workbench
Este documento fornece informações sobre como o Azure Machine Learning Workbench utiliza o Git para garantir reproducibility na sua experimentação de ciência de dados. Também são fornecidas instruções sobre como associar o seu projeto com uma nuvem repositório de Git.

## <a name="introduction"></a>Introdução
Azure Workbench de aprendizagem máquina concebido com a integração de Git do zero cópias de segurança. Quando criar um novo projeto, a pasta do projeto é automaticamente "inicializado pelo Git" como um repositório de Git local (repositório) enquanto um segundo repositório de Git local oculto também é criado com uma sucursal com o nome _AzureMLHistory / < project_GUID >_ para manter um registo das alterações de pasta do projeto para cada execução. 

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


> [!TIP]
> Certifique-se de início de sessão com a conta do Azure Active Directory (AAD) utilizada para aceder ao Workbench do Azure Machine Learning. Caso contrário, o novo projeto de equipa poderá fim-se sob o ID de inquilino errado e do Azure Machine Learning não poderá encontrá-lo. Neste caso, terá de utilizar a interface de linha de comandos e fornecer o token VSTS.

Assim que for criado o projeto de equipa, está pronto para mudar para o passo seguinte.

Para navegar diretamente para o projeto de equipa que acabou de criar, o URL é `https://<team_project_name>.visualstudio.com`.

> [!NOTE]
> Atualmente, o Azure Machine Learning suporta apenas vazios Git repos com nenhuma ramo principal. Da interface da linha de comandos, pode utilizar – force argumento para eliminar primeiro o ramo principal. 

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>Passo 3. Criar um novo projeto do Azure ML com um repositório de Git remoto
Inicie o Workbench do Azure ML e criar um novo projeto. Preencha a caixa de texto do repositório de Git com o URL do repositório de VSTS Git que obtém do passo 2. Normalmente, parece este: http://<vsts_account_name>.visualstudio.com/_git/<project_name>

![Criar projeto do Azure ML com o repositório de Git](media/using-git-ml-project/create_project_with_git_rep.png)

Agora é criado um novo projeto do Azure ML com a integração do repositório de Git remota ativado e pronto para começar. A pasta do projeto é sempre inicializado pelo Git como um repositório de Git local. E o Git _remoto_ está definido para o repositório de VSTS Git remoto para consolidações podem ser enviadas para o repositório de Git remoto.

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>Passo 3.a associar um projeto do Azure ML existente com um repositório de VSTS Git
Opcionalmente, pode também criar um projeto do Azure ML sem um repositório de VSTS Git e apenas pode confiar no repositório de Git local para instantâneos do histórico de execução. E pode associar um repositório de VSTS Git mais tarde este projeto do Azure ML existente utilizando o seguinte comando:

```azurecli
# make sure you are in the project path so CLI has context of your current project
az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name
```

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>Passo 4. Captura de instantâneos de projeto no repositório de Git
Agora pode executar alguns é executado no projeto, certifique-algumas alterações entre o é executado. Pode fazê-a aplicação de ambiente de trabalho, ou do CLI utilizando `az ml experiment submit` comando. Para obter mais detalhes, pode seguir o [tutorial classificar Iris](tutorial-classifying-iris-part-1.md). Para cada execução, se não houver qualquer alteração efetuada em todos os ficheiros na pasta do projeto, um instantâneo da pasta do projeto todo é consolidado e enviado para o repositório de Git remoto. Pode ver os ramos e consolidações ao navegar para o URL do repositório de VSTS Git.

![ramo de histórico de execução](media/using-git-ml-project/run_history_branch.png)

## <a name="step-5-restore-a-previous-project-snapshot"></a>Passo 5. Restaurar um instantâneo anterior do projeto 
Para restaurar a pasta do projeto todo para o estado de um instantâneo estado de projeto da histórico de execução anterior, de AML Workbench.
1. Clique em **executa** na atividade de barra (ícone glass hora).
2. Do **executar lista** ver, clique em execução que pretende restaurar.
3. Do **executar detalhe** ver, clique em **restaurar**.

![ramo de histórico de execução](media/using-git-ml-project/restore_project.png)

Em alternativa, pode utilizar o seguinte comando na janela da CLI do Azure ML Workbench.

```azurecli
# discover the run I want to restore snapshot from:
az ml history list -o table

# restore the snapshot from a particular run
az ml project restore --run-id <run_id>
```

Ao executar este comando, iremos irá substituir a pasta do projeto todo com o instantâneo tirado quando esse específico run foi arrancou. Isto significa que poderá **perder todas as alterações** na pasta do projeto atual. Por isso, tenha muito cuidado quando executar este comando.

## <a name="step-6-use-the-master-branch"></a>Passo 6. Utilize o ramo principal
Uma forma para evitar a perda acidental o seu estado atual do projeto, é para consolidar o projeto para o ramo principal do repositório de Git. Pode utilizar diretamente Git de linha de comandos (ou a outra favorita Git ferramenta de cliente da preferência) a funcionar o ramo principal. Por exemplo:

```
# make sure you are on the master branch
git checkout master

# stage all changes
git add -A

# commit all changes locally on the master branch
git commit -m 'this is my updates so far'

# push changes into the remote VSTS Git repo master branch.
git push origin master
```

Agora, pode restaurar em segurança projeto para um instantâneo anterior seguinte passo 5, sabendo que, pode sempre voltar para a consolidação apenas efetuadas no principal ramo.

## <a name="authentication"></a>Autenticação
Se confiar apenas as funções de histórico de execução do Azure ML para tirar instantâneos do projeto e o restauro, não precisa de preocupar com a autenticação do repositório de Git. É tratado pela camada de serviço de experimentação.

No entanto, se utilizar as suas próprias ferramentas de Git para gerir o controlo de versão, terá de processar corretamente a autenticação contra o repositório de Git remoto no VSTS. Ou seja, terá de configurar a autenticação com o repositório de Git no computador local antes de pode emitir comandos de Git contra desse repositório de Git remoto. 

É a forma mais fácil de fazê-lo, para criar um par de chaves SSH e carrega a parte de chave pública para as definições de segurança do repositório de Git.

### <a name="generate-ssh-key"></a>Gerar a chave SSH 
Primeiro vamos gerar um par de chaves SSH no seu computador.

#### <a name="on-windows"></a>No Windows:
Inicie a aplicação de ambiente de trabalho de GUI do Git no Windows e, em _ajudar_ menu, clique em _Mostrar chave SSH_.

![SSH chave](media/using-git-ml-project/git_gui.png)

Copie o SSH para a área de transferência.

#### <a name="on-macos"></a>No macOS:
Passos rápidos a partir da shell de comandos:
```
# generate the SSH key
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# start the SSH agent in the background
$ eval "$(ssh-agent -s)"

# add newly generated SSH key to the SSH agent
$ ssh-add -K ~/.ssh/id_rsa

# display the public key so you can copy it.
$ more ~/.ssh/id_rsa.pub
```
Podem encontrar mais passos detalhes em [neste artigo do GitHub](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).

### <a name="upload-public-key-to-git-repo"></a>Carregar a chave pública para o repositório de Git
Aceda à sua conta de Visual Studio home page: https://<vsts_account_name>.visualstudio.com e iniciar sessão, em seguida, clique em segurança no seu avatar.

Em seguida, adicionar uma chave pública SSH, cole a chave pública SSH que obter do passo anterior e atribua um nome. Pode adicionar várias chaves aqui.

Agora, pode emitir comandos de Git localmente contra o repositório de remover com nenhum outro explícita é necessária a autenticação.

### <a name="read-more"></a>Saiba mais
Siga estes dois artigos (ou abordagem pode funcionar) para obter mais detalhes sobre como ativar a autenticação local para o repositório de Git remoto no VSTS.
- [Utilize a autenticação de chave SSH](https://www.visualstudio.com/en-us/docs/git/use-ssh-keys-to-authenticate)
- [Utilizar credencial de Git gestores](https://www.visualstudio.com/en-us/docs/git/set-up-credential-managers)


## <a name="next-steps"></a>Passos seguintes
Saiba como utilizar o processo de ciência de dados de agrupamento para organizar a estrutura do projeto, consulte [estruturar um projeto com TDSP](how-to-use-tdsp-in-azure-ml.md)
