---
title: "Tutorial artigo para funcionalidades de pré-visualização do Azure Machine Learning - Interface de linha de comandos | Microsoft Docs"
description: "Este tutorial percurso através de todos os passos necessários para concluir uma Iris classificação ponto-a-ponto da interface de linha de comandos."
services: machine-learning
author: ahgyger
ms.author: ahgyger, ritbhat
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 453c774c97b77dd7829a50fa5e5668d06f817a1d
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Tutorial: Classificar Iris utilizando a interface de linha de comandos
Os serviços do Azure Machine Learning (pré-visualização) são uma solução de análise avançada e ciência de dados ponto a ponto integrada para os cientistas de dados profissionais prepararem dados, desenvolverem experimentações e implementarem modelos à escala da cloud.

Neste tutorial, a aprende a utilizar as ferramentas de interface de linha de comandos (CLI) em funcionalidades de pré-visualização do Azure Machine Learning para: 
> [!div class="checklist"]
> * Configurar uma conta de experimentação e criar uma área de trabalho
> * Criar um projeto
> * Submeter uma experimentação para vários destinos de computação
> * Promover e registar um modelo preparado
> * Implementar um serviço web para pontuar novos dados

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
- Precisa de acesso a uma subscrição do Azure e permissões para criar recursos nessa subscrição. 
- Tem de instalar a aplicação do Azure Machine Learing Workbench seguindo o [instalar e criar o guia de introdução](quickstart-installation.md). 

  >[!NOTE]
  >Só tem de instalar o Azure Machine Learning Workbench do localmente. Só tem de seguir os passos nas secções designada por instalar o Azure Machine Learning Workbench, desde a criação de conta e criar um novo projeto passos irão ser feitos através da linha de comandos neste artigo.
 
## <a name="getting-started"></a>Introdução
Interface de linha de comandos do Azure Machine Learning (CLI) permite-lhe efetuar todas as tarefas necessárias para um fluxo de trabalho de ciências de dados ponto-a-ponto. Pode aceder as ferramentas da CLI das seguintes formas:

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>Opção 1. Inicie a CLI do AZURE ML a partir da caixa de diálogo de início de sessão do Workbench do Azure ML
Quando iniciar o Workbench do Azure ML e iniciar sessão pela primeira vez, e, se ainda não tiver acesso a uma conta de experimentação, é-lhe apresentado o ecrã seguinte:

![foi encontrada nenhuma conta](media/tutorial-iris-azure-cli/no_account_found.png)

Clique em de **janela de linha de comandos** ligação na caixa de diálogo para iniciar a janela de linha de comandos.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>Opção 2. Inicie a CLI do Azure ML da aplicação do Azure ML Workbench
Se já tiver acesso a uma conta de experimentação, pode iniciar sessão com êxito. E, em seguida, pode abrir a janela de linha de comandos clicando em **ficheiro** --> **abrir este comando Prompt** menu.

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>Opção 3. Ativar a CLI do AZURE ML numa janela da linha de comandos arbitrária
Também pode ativar a CLI do AZURE ML em qualquer janela de linha de comandos. Basta inicie uma janela de comandos e introduza os seguintes comandos:

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
Para efetuar a alteração permanente, pode utilizar `SETX` no Windows. Para macOS, pode utilizar `setenv`.

>[!TIP]
>Pode ativar a CLI do Azure na sua janela de terminal favorita definindo as variáveis de ambiente acima.

## <a name="step-1-log-in-to-azure"></a>Passo 1. Iniciar sessão no Azure
O primeiro passo consiste em abrir o CLI da aplicação AMLWorkbench (ficheiro > abrir a linha de comandos). Se o fizer, garante que utilizamos o ambiente do python à direita e temos os comandos da CLI do ML disponíveis. 

Em seguida, é necessário definir o contexto de direito do CLI para aceder e gerir recursos do Azure.
 
```azure-cli
# log in
$ az login

# list all subscriptions
$ az account list -o table

# set the current subscription
$ az account set -s <subscription id or name>
```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>Passo 2. Criar uma nova conta de experimentação do Azure Machine Learning e área de trabalho
Vamos começar por criar uma nova conta de experimentação e uma nova área de trabalho. Consulte [conceitos do Azure Machine Learning](overview-general-concepts.md) para obter mais detalhes sobre as contas de experimentação e áreas de trabalho.

> [!NOTE]
> Contas de experimentação precisam de uma conta de armazenamento, que é utilizada para armazenar as saídas da sua experimentação executa. O nome da conta de armazenamento tem de ser globalmente exclusivo no Azure porque não há um url com associados. Se não especificar uma conta de armazenamento existente, o nome da sua experimentação conta é utilizada para criar uma nova conta de armazenamento. Certifique-se de que utiliza um nome exclusivo ou obterá um erro, tal como _"conta de armazenamento com o nome \<storage_account_name > já estiver atribuído."_ Em alternativa, pode utilizar o `--storage` argumento para fornecer uma conta de armazenamento existente.

```azure-cli
# create a resource group 
$ az group create --name <resource group name> --location <supported Azure region>

# create a new experimentation account with a new storage account
$ az ml account experimentation create --name <experimentation account name> --resource-group <resource group name>

# create a new experimentation account with an existing storage account
$ az ml account experimentation create --name <experimentation account name>  --resource-group <resource group name> --storage <storage account Azure Resource ID>

# create a workspace in the experimentation account
az ml workspace create --name <workspace name> --account <experimentation account name> --resource-group <resource group name>
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>Passo 2.a (opcional) partilhar uma área de trabalho com colega de trabalho
Aqui vamos explorar como partilhar o acesso a uma área de trabalho com um colega de trabalho. Os passos para aceder a uma conta de experimentação ou de um projeto de partilhar será o mesmo. Apenas a forma de obter o ID de recurso do Azure teriam de ser atualizados.

```azure-cli
# find the workspace Azure Resource ID
$az ml workspace show --name <workspace name> --account <experimentation account name> --resource-group <resource group name>

# add Bob to this workspace as a new owner
$az role assignment create --assignee bob@contoso.com --role owner --scope <workspace Azure Resource ID>
```

> [!TIP]
> `bob@contoso.com`no comando acima tem de ser um identidade do Azure AD no diretório onde a subscrição atual pertence.

## <a name="step-3-create-a-new-project"></a>Passo 3. Criar um novo projeto
Nosso passo seguinte consiste em criar um novo projeto. Existem várias formas de começar com um novo projeto.

### <a name="create-a-new-blank-project"></a>Criar um novo projeto em branco

```azure-cli
# create a new project
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path>
```

### <a name="create-a-new-project-with-a-default-project-template"></a>Criar um novo projeto com um modelo de projeto predefinido
Pode criar um novo projeto com um modelo predefinido.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Criar um novo projeto associado uma nuvem repositório de Git
Podemos criar um novo projeto associado um repositório de Git VSTS (Visual Studio Team serviço). Sempre que for submetida uma experimentação, um instantâneo da pasta do projeto todo é submetido para o repositório de Git remoto. Consulte [repositório de Git utilizando com um projeto do Azure Machine Learning Workbench](using-git-ml-project.md) para obter mais detalhes.

> [!NOTE]
> O Azure Machine Learning só suporta vazios repos de Git criados no VSTS.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --repo <VSTS repo URL>
```
> [!TIP]
> Se estiver a receber um erro "o url de repositório poderá ser inválida ou o utilizador poderá não ter acesso", pode criar um token de segurança no VSTS (em _segurança_, _adicionar tokens de acesso pessoal_ menu) e utilizar o `--vststoken`argumento ao criar o projeto. 

### <a name="sample_create"></a>Criar um novo projeto a partir de uma amostra
Neste exemplo, vamos criar um novo projeto através de um projeto de exemplo como um modelo.

```azure-cli
# List the project samples, find the Classifying Iris sample
$ az ml project sample list

# Create a new project from the sample
az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```
Depois do projeto ser criado, utilize `cd` comandos, introduza o diretório de projeto.

## <a name="step-4-run-the-training-experiment"></a>Passo 4, execute a experimentação de preparação 
Os passos abaixo assumem que tem um projeto com o exemplo de Iris (consulte [criar um novo projeto a partir de uma amostra online](#sample_create)).

### <a name="prepare-your-environment"></a>Preparar o ambiente 
Para o exemplo de Iris, é necessário instalar matplotlib.

```azure-cli
$ pip install matplotlib
```

###  <a name="submit-the-experiment"></a>Submeter a experimentação

```azure-cli
# Execute the file
$ az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>Iterar a sua experiência com descendente taxas regularization
Com alguns creativity é simple juntar-se de um script de Python que submete experimentações com taxas de regularization diferentes. (Poderá ser necessário editar o ficheiro para apontar para o caminho de projeto correto.)

```azure-cli
$ python run.py
```

## <a name="step-5-view-run-history"></a>Passo 5. Ver o histórico de execuções
Comando a seguir apresenta uma lista de todas as execuções de anteriores foi executadas. 

```azure-cli
$ az ml history list -o table
```
Executar o comando acima apresentará uma lista de todas as execuções pertencentes a este projeto. Pode ver que métricas de taxa de precisão e regularization estão listadas demasiado. Este disponibilizar facilitam a identificar o melhor executar na lista.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>Passo anexo de vista 5.a criado por uma execução especificada 
Para ver o anexo associado um determinado executar, podemos utilizar o comando de informações de histórico de execução. Localize um id de execução de um execute específico a partir da lista acima.

```azure-cli
$ az ml history info --run <run id> --artifact driver_log
```

Para transferir os artefactos de uma execução, pode utilizar o comando abaixo:

```azure-cli
# Stream a given attachment 
$ az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>Passo 6. Promover artefactos de execução 
Uma das execuções Tornamos tem uma melhor AUC, pelo que iremos pretender utilizá-la para criar um serviço web classificação para implementar em produção. Para tal, necessitamos primeiro promover os artefactos para um elemento.

```azure-cli
$ az ml history promote --run <run id> --artifact-path outputs/model.pkl --name model.pkl
```

Esta ação cria um `assets` pasta no diretório do seu projeto com um `model.pkl.link` ficheiro. Este ficheiro de ligação é utilizado para fazer referência a um recurso promovido.

## <a name="step-7-download-the-files-to-be-operationalized"></a>Passo 7. Transferir os ficheiros para ser operacionalizado
Iremos agora, tem de transferir o modelo de promovida para que podermos utilizá-los para criar o nosso serviço web de predição. 

```azure-cli
$ az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-setup-your-model-management-environment"></a>Passo 8. Configuração do ambiente de gestão de modelo 
Vamos criar um ambiente para implementar os serviços web. É possível executar o serviço web no computador local utilizando o Docker. Ou implementá-la a um cluster de ACS para operações de grande escala. 

```azure-cli
# Create new local operationalization environment
$ az ml env setup -l <supported Azure region> -n <env name>
# Once setup is complete, set your environment for current context
$ az ml env set -g <resource group name> -n <env name>
```

## <a name="step-9-create-a-model-management-account"></a>Passo 9. Criar uma conta de gestão de modelo 
É necessária uma conta de gestão de modelo para implementar e controlar os seus modelos na produção. 

```azure-cli
$ az ml account modelmanagement create -n <model management account name> -g <resource group name> -l <supported Azure region>
```

## <a name="step-10-create-a-web-service"></a>Passo 10. Criar um serviço Web
Em seguida, criamos um serviço web que devolve uma predição utilizando o modelo que foi implementada. 

```azure-cli
$ az ml service create realtime -m asset_download/model.pkl -f score.py -r python –n <web service name>
```

## <a name="step-10-run-the-web-service"></a>Passo 10. Executar o serviço web
Com o id do serviço web da saída do passo anterior, mas pode chamar o serviço web e testá-lo. 

```azure-cli
# Get web service usage infomration
$ az ml service usage realtime -i <web service id>

# Call the web service with the run command:
$ az ml service run realtime -i <web service id> -d <input data>
```

## <a name="deleting-all-the-resources"></a>Eliminar todos os recursos 
Vamos concluir este tutorial, eliminando todos os recursos que foi criado, a menos que pretenda manter a funcionar no mesmo! 

Para tal, basta que eliminar o grupo de recursos que contém todos os nossos recursos. 

```azure-cli
az group delete --name <resource group name>
```

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a utilizar as funcionalidades de pré-visualização do Azure Machine Learning para 
> [!div class="checklist"]
> * Configure uma conta de experimentação, criação de área de trabalho
> * Criar projetos
> * Submeter experimentações para várias de computação de destino
> * Promover e registar um modelo preparado
> * Criar uma conta de gestão de modelo para a gestão de modelo
> * Criar um ambiente para implementar um serviço web
> * Implementar um serviço web e o modelo de pontuação com novos dados