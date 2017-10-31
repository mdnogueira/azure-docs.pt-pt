---
title: "Início Rápido de Instalação para os serviços do Azure Machine Learning | Microsoft Docs"
description: "Este Início Rápido mostra como criar recursos do Azure Machine Learning e como instalar o Azure Machine Learning Workbench."
services: machine-learning
author: hning86
ms.author: haining, raymondl, chhavib
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: hero-article
ms.date: 10/13/2017
ms.openlocfilehash: 07d06e4de95fcc562bcc76ac5cc4f5cd3483ba6d
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="create-azure-machine-learning-preview-accounts-and-install-azure-machine-learning-workbench"></a>Criar contas de pré-visualização do Azure Machine Learning e instalar o Azure Machine Learning Workbench
O Azure Machine Learning é uma solução de análise avançada e de ciência de dados integrada e completa. Ajuda os cientistas de dados profissionais a preparar dados, desenvolver experimentações e implementar modelos à escala da cloud.

Este Início Rápido mostra-lhe como criar contas de experimentação e de gestão de modelos na Pré-visualização do Azure Machine Learning. Também mostra como instalar a aplicação de computador Azure Machine Learning Workbench e as ferramentas da CLI. Depois, vamos fazer uma visita guiada às funcionalidades de Pré-visualização do Azure Machine Learning com o [conjunto de dados Flor de Íris](https://en.wikipedia.org/wiki/iris_flower_data_set), de modo a criar um modelo para prever o tipo de íris com base em algumas das suas características físicas.  

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Atualmente, só pode instalar a aplicação de computador Azure Machine Learning Workbench nos seguintes sistemas operativos: 
- Windows 10
- Windows Server 2016
- macOS Sierra
- macOS High Sierra

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-azure-machine-learning-accounts"></a>Criar contas do Azure Machine Learning
Utilize o portal do Azure para aprovisionar contas do Azure Machine Learning: 
1. Selecione o botão (+) **Novo**, no canto superior esquerdo do portal.

2. Introduza **Machine Learning** na barra de pesquisa. Selecione o resultado da pesquisa com o nome **Experimentação do Machine Learning (pré-visualização)**.  Clique no ícone de estrela para tornar esta seleção num favorito no portal do Azure.

   ![Pesquisa por Azure Machine Learning](media/quickstart-installation/portal-more-services.png)

3. Selecione **+ Adicionar** para configurar uma conta de Experimentação do Machine Learning nova. O formulário detalhado abre-se.

   ![Conta de Experimentação do Machine Learning](media/quickstart-installation/portal-create-experimentation.png)

4. Preencha o formulário da Experimentação do Machine Learning com as seguintes informações:

   Definição|Valor sugerido|Descrição
   ---|---|---
   Nome da conta de Experimentação | _Nome exclusivo_ |Escolha um nome exclusivo que identifique a sua conta. Pode utilizar o seu nome ou o nome de um departamento ou projeto, aquele que melhor identifique a experimentação. O nome deve ter entre 2 e 32 carateres. Deve incluir apenas carateres alfanuméricos e o caráter de travessão (–). 
   Subscrição | _A sua subscrição_ |Escolha a subscrição do Azure que quer utilizar para a experimentação. Se tiver múltiplas subscrições, escolha a subscrição adequada na qual o recurso é cobrado.
   Grupo de recursos | _O seu grupo de recursos_ | Pode criar um nome de grupo de recursos novo ou utilizar um já existente na sua subscrição.
   Localização | _A região mais próxima dos seus utilizadores_ | Escolha a localização que esteja mais próxima dos seus utilizadores e dos recursos de dados.
   Número de utilizadores | 2 | Introduza o número de utilizadores. Esta seleção afeta o [preço](https://azure.microsoft.com/pricing/details/machine-learning/). Os dois primeiros utilizadores são gratuitos. Utilize dois utilizadores para efeitos deste Início Rápido. Pode atualizar o número de utilizadores mais tarde, conforme necessário, no portal do Azure.
   Conta de armazenamento | _Nome exclusivo_ | Selecione **Criar nova** e forneça um nome para criar uma conta de armazenamento do Azure. Em alternativa, selecione **Utilizar existente** e selecione a conta de armazenamento existente na lista pendente. A conta de armazenamento é necessária e é utilizada para armazenar artefactos de projetos e dados do histórico de execuções. 
   Área de Trabalho para a conta de Experimentação | _Nome exclusivo_ | Indique um nome para a área de trabalho nova. O nome deve ter entre 2 e 32 carateres. Deve incluir apenas carateres alfanuméricos e o caráter de travessão (–).
   Atribuir proprietário à área de trabalho | _A sua conta_ | Selecione a sua própria conta como o proprietário da área de trabalho.
   Criar conta de Gestão de Modelos | *selecionar* | Como parte da experiência de criação da conta de Experimentação, tem a opção de também criar a conta de Gestão de Modelos do Machine Learning. Este recurso é utilizado quando estiver pronto para implementar e gerir os seus modelos como serviços Web em tempo real. Recomendamos que crie a conta de Gestão de Modelos ao mesmo tempo que a conta de Experimentação.
   Nome da conta | _Nome exclusivo_ | Escolha um nome exclusivo que identifique a sua conta de Gestão de Modelos. Pode utilizar o seu nome ou o nome de um departamento ou projeto, aquele que melhor identifique a experimentação. O nome deve ter entre 2 e 32 carateres. Deve incluir apenas carateres alfanuméricos e o caráter de travessão (–). 
   Escalão de preços da Gestão de Modelos | **DEV/TEST** | Selecione **Nenhum escalão de preço selecionado** para especificar o escalão de preço da sua conta de Gestão de Modelos nova. Para poupar nos custos, selecione o escalão de preço **DEVTEST** se estiver disponível na sua subscrição (disponibilidade limitada). Caso contrário, selecione o escalão S1, para beneficiar de uma poupança nos custos. Clique em **Selecionar** para guardar a escolha do escalão de preço. 
   Afixar ao dashboard | _selecionar_ | Selecione a opção **Afixar ao dashboard** para permitir uma fácil monitorização da conta de Experimentação do Machine Learning na página de rosto do dashboard do portal do Azure.

5. Selecione **Criar** para iniciar o processo de criação.

6. Na barra de ferramentas do portal do Azure, clique em **Notificações** (ícone de sino) para monitorizar o processo de implementação. 

   A notificação mostra **Implementação em curso**. O estado é alterado para **Implementação bem-sucedida** quando estiver concluída. A página da sua conta de Experimentação do Machine Learning abre-se após a conclusão bem-sucedida.
   
   ![Notificações do portal do Azure](media/quickstart-installation/portal-notification.png)

Agora, consoante o sistema operativo que utilizar no seu computador local, siga uma das duas secções seguintes para instalar o Azure Machine Learning Workbench. 

## <a name="install-azure-machine-learning-workbench-on-windows"></a>Instalar o Azure Machine Learning Workbench em Windows
Instale o Azure Machine Learning Workbench no seu computador com Windows 10, Windows Server 2016 ou mais recente.

1. Transfira o instalador mais recente do Azure Machine Learning Workbench [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi).

2. Faça duplo clique no instalador que transferiu **AmlWorkbenchSetup.msi** a partir do Explorador de Ficheiros.

   >[!IMPORTANT]
   >Transfira o instalador completamente para o disco e execute-o aí. Não o execute diretamente a partir do widget de transferência do browser.

3. Siga as instruções no ecrã para concluir a instalação.

   O instalador transfere todos os componentes dependentes necessários, tais como o Python, o Miniconda e outras bibliotecas associadas. A instalação pode demorar cerca de meia hora para concluir todos os componentes. 

4. O Azure Machine Learning Workbench é agora instalado no diretório seguinte:
   
   `C:\Users\<user>\AppData\Local\AmlWorkbench`

## <a name="install-azure-machine-learning-workbench-on-macos"></a>Instalar o Azure Machine Learning Workbench em macOS
Instale o Azure Machine Learning Workbench no seu computador com macOS Sierra.

1. Utilize o [Homebrew](http://brew.sh) para instalar a biblioteca openssl. Para obter mais informações, veja [Pré-requisitos para .NET Core em Mac](https://docs.microsoft.com/dotnet/core/macos-prerequisites).
   ```
   # install Homebrew first if you don't have it already
   /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

   # install latest openssl needed for .NET Core 1.x
   brew update
   brew install openssl
   mkdir -p /usr/local/lib
   ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
   ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
   ```

2. Transfira o instalador mais recente do Azure Machine Learning Workbench [AmlWorkbench.dmg](https://aka.ms/azureml-wb-dmg).

   >[!IMPORTANT]
   >Transfira o instalador completamente para o disco e execute-o aí. Não o execute diretamente a partir do widget de transferência do browser.

3. Faça duplo clique ni instalador que transferiu **AmlWorkbench.dmg** a partir do Finder.

4. Siga as instruções no ecrã para concluir a instalação.

   O instalador transfere todos os componentes dependentes necessários, tais como o Python, o Miniconda e outras bibliotecas associadas. A instalação pode demorar cerca de meia hora para concluir todos os componentes. 

5. O Azure Machine Learning Workbench é agora instalado no diretório seguinte: 

   `/Applications/AmlWorkbench.app`

## <a name="run-azure-machine-learning-workbench-to-sign-in-for-the-first-time"></a>Executar o Azure Machine Learning Workbench para iniciar sessão pela primeira vez
1. Assim que o processo de instalação estiver concluído, selecione o botão **Iniciar Workbench**, no último ecrã do instalador. Caso tenha fechado o instalador, encontre o atalho para o Machine Learning Workbench no ambiente de trabalho e no menu **Iniciar** com o nome **Azure Machine Learning Workbench** para iniciar a aplicação.

2. Inicie sessão no Workbench com a mesma conta que utilizou anteriormente para aprovisionar os recursos do Azure. 

3. Depois de concluído o início de sessão, o Workbench tenta encontrar as contas de Experimentação do Machine Learning que criou antes. Procura todas as subscrições do Azure às quais a sua credencial tem acesso. Quando é encontrada pelo menos uma conta de Experimentação, o Workbench é aberto com essa conta. Em seguida, apresenta as áreas de trabalho e os projetos encontrados na mesma. 

   >[!TIP]
   > Se tiver acesso a mais de uma conta de Experimentação, pode mudar para outra ao selecionar o ícone de avatar no canto inferior esquerdo da aplicação Workbench.

Para obter mais informações sobre como criar um ambiente para implementar os seus serviços Web, veja [Configuração do Ambiente de Implementação](deployment-setup-configuration.md).

## <a name="create-a-new-project"></a>Criar um novo projeto
1. Inicie a aplicação Azure Machine Learning Workbench e inicie sessão. 

2. Selecione **Ficheiro** > **Novo Projeto** (ou selecione o sinal **+** no painel **PROJETOS**). 

3. Preencha as caixas **Nome do projeto** e **Diretório do projeto**. **Descrição do projeto** é opcional, mas útil. Deixe a caixa **URL do Repositório do GIT Visualstudio.com** em branco por agora. Escolha uma área de trabalho e selecione **Classificar Íris** como o modelo do projeto.

   >[!TIP]
   >Opcionalmente, pode preencher a caixa de texto do repositório do Git com o URL de um repositório do Git que esteja alojado num projeto do [Visual Studio Team Services](https://www.visualstudio.com). Este repositório do Git tem de já existir e tem de estar vazio, sem ramificações principais. Também tem de ter acesso de escrita ao mesmo. Adicionar um repositório do Git agora permite-lhe ativar cenários de roaming e partilha mais tarde. [Leia mais](using-git-ml-project.md).

4. Selecione o botão **Criar** para criar o projeto. É criado e aberto um projeto novo. Neste momento, pode explorar a home page, as origens de dados, os blocos de notas e os ficheiros de código de origem do projeto. 

    >[!TIP]
    >Também pode abrir o projeto no Visual Studio Code ou noutros editores mediante a simples configuração de uma ligação de IDE (ambiente de desenvolvimento integrado) e, em seguida, abrir o diretório do projeto nos mesmos. [Leia mais](how-to-configure-your-IDE.md). 

## <a name="run-a-python-script"></a>Executar um script de Python
Vamos executar um script no seu computador local. 

1. Cada projeto abre-se na sua própria página de **Dashboard do Projeto**. Selecione **local** como destino de execução, a partir da barra de comandos junto à parte superior da aplicação e **iris_sklearn.py** como script a executar. Existem outros ficheiros incluídos no exemplo, que pode ver mais tarde. 

   ![Barra de comandos](media/quickstart-installation/run_control.png)

2. Na caixa de texto **Argumentos**, introduza **0,01**. Este número é utilizado no código para definir a taxa de regularização. Trata-se de um valor utilizado para configurar a forma como é preparado o modelo de regressão linear. 

3. Selecione o botão **Executar** para iniciar a execução de **iris_sklearn.py** no seu computador. 

   Este código utiliza o algoritmo de [regressão logística](https://en.wikipedia.org/wiki/logistic_regression) da popular biblioteca de Python [scikit-learn](http://scikit-learn.org/stable/index.html) para criar o modelo.

4. O painel **Tarefas** surge do lado direito, se ainda não estiver visível, e é adicionado um trabalho **iris_sklearn** ao painel. O estado passa de **A submeter** para **Em execução** à medida que o trabalho começa a ser executado e, depois, para **Concluído** passados alguns segundos. 

   Parabéns! Executou com êxito um script de Python no Azure Machine Learning Workbench.

6. Repita os passos 2 a 4 várias vezes. Utilize sempre valores de argumentos diferentes entre **10** e **0,001**.

## <a name="view-run-history"></a>Ver o histórico de execuções
1. Aceda à vista **Execuções** e selecione **iris_sklearn.py** na lista de execuções. É aberto o dashboard do histórico de execuções de **iris_sklearn.py**. Mostra todas as execuções que foram executadas em **iris_sklearn.py**. 

   ![Dashboard do histórico de execuções](media/quickstart-installation/run_view.png)

2. O dashboard do histórico de execuções também mostra as principais métricas, um conjunto de gráficos predefinidos e uma lista de métricas para cada execução. Pode personalizar esta vista ao ordenar, filtrar e ajustar as configurações. Basta selecionar o ícone de configuração ou o ícone de filtro.

   ![Métricas e gráficos](media/quickstart-installation/run_dashboard.png)

3. Selecione uma execução concluída e poderá ver uma vista detalhada para essa execução específica. Os detalhes incluem métricas adicionais, os ficheiros que foram produzidos e outros registos potencialmente úteis.

## <a name="next-steps"></a>Passos seguintes
Criou agora com êxito uma conta de Experimentação do Azure Machine Learning e uma conta de Gestão de Modelos do Azure Machine Learning. Instalou a aplicação de computador Azure Machine Learning Workbench e a interface de linha de comandos. Criou um projeto novo, criou um modelo ao executar um script e explorou o histórico de execuções do script.

Para obter uma experiência mais aprofundada para este fluxo de trabalho, incluindo como implementar o seu modelo de Íris, siga o tutorial *Classificar Íris* completo. O tutorial contém os passos detalhados para [preparação de dados](tutorial-classifying-iris-part-1.md), [experimentação](tutorial-classifying-iris-part-2.md) e [gestão de modelos](tutorial-classifying-iris-part-3.md). 

> [!div class="nextstepaction"]
> [Tutorial “Classifying Iris”](tutorial-classifying-iris-part-1.md) (“Classificar Íris”)
