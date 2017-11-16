---
title: "Equipa dados ciência processo equipa levar tarefas - Azure | Microsoft Docs"
description: "Uma descrição das tarefas de uma equipa levar num projeto de equipa de ciência de dados."
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
ms.date: 11/13/2017
ms.author: bradsev;
ms.openlocfilehash: 58e8bfeb547fbcd7f472fbed50073b1ca3176b72
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="team-lead-tasks"></a>Tarefas de fabrico de equipa

Este tópico de destaca as tarefas que é uma antecedência de equipa esperado concluir a sua equipa de ciência de dados. O objetivo consiste em estabelecer o ambiente de colaboração de equipa que uniformiza no [o processo de ciência de dados de equipa](overview.md) (TDSP). TDSP é uma metodologia de ciência de dados seja ágil e interativo para fornecer soluções de Análise Preditiva e aplicações inteligentes de forma eficiente. Foi concebido para ajudar a melhorar a colaboração e aprendizagem de equipa. O processo é uma distillation das melhores práticas e estruturas de ambos os Microsoft, bem como da indústria, necessário para a implementação bem sucedida do iniciativas de ciência de dados para ajudar as empresas totalmente tenha em consideração as vantagens dos seus programas de análise. Para uma descrição das funções de pessoal e das respetivas tarefas associadas são processados por uma equipa de ciência de dados uniformizar neste processo, consulte o artigo [funções do processo de ciência de dados de equipa e tarefas](roles-tasks.md).

A **equipa levar** gere uma equipa na unidade de ciência de dados de uma empresa. Uma equipa é composta por vários cientistas de dados. Para a unidade de ciência de dados com apenas um pequeno número de cientistas de dados, o **Gestor grupo** e **equipa levar** poderá ser a mesma pessoa ou poderia delegar as respetivas tarefas a um substituto. Mas não alterar as tarefas próprios. O fluxo de trabalho para as tarefas a ser concluídos por leva de equipa, a configurar este ambiente estão representados na figura seguinte:

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] As tarefas em blocos 1 e 2 da figura são necessários se estiver a utilizar serviços de equipa do Visual Studio (VSTS) como o código de plataforma de alojamento e pretender ter um projeto de equipa separada para o seus próprios equipa. Depois de concluídas estas tarefas, todos os repositórios da sua equipa podem ser criados este projeto de equipa. 

Depois de vários pré-requisitos tarefas especificadas na secção seguinte são satisfeitas pelo Gestor de grupo, existem cinco tarefas principais (algumas opcional) que concluir este tutorial. Estas tarefas correspondem principal numeradas secções deste tópico:

1. Criar um **projeto de equipa** no servidor VSTS do grupo do grupo e dois repositórios de equipa no projeto:
    - **Repositório de ProjectTemplate** 
    - **Repositório de TeamUtilities**
2. A equipa de seed **ProjectTemplate** repositório do **GroupProjectTemplate** repositório que tenha sido configurado, o Gestor do grupo. 
3. Crie recursos de dados e análise da equipa:
    - Adicionar os utilitários de equipa específicos para o **TeamUtilities** repositório. 
    - (Opcional) Criar um **file storage do Azure** a ser utilizado para armazenar os recursos de dados que podem ser úteis para a equipa de toda. 
4. (Opcional) Monte o file storage do Azure para o **máquinas de virtuais de ciência de dados** (DSVM) da equipa de levar e adicionar os recursos de dados no mesmo.
5. Configurar o **controlo de segurança** ao adicionar os membros da equipa e configurar os seus privilégios.

>[AZURE.NOTE] Iremos descrevem os passos necessários para configurar um ambiente de equipa TDSP utilizando VSTS nas instruções seguintes. Especificamos como realizar estas tarefas com VSTS, uma vez que é a forma como implementamos TDSP na Microsoft. Se for utilizado outro código de plataforma de alojamento para o grupo, as tarefas que necessitam de ser concluídos pela antecedência de equipa, geralmente, não alteram. Mas a forma para concluir estas tarefas vai ser diferentes.

## <a name="repositories-and-directories"></a>Repositórios e diretórios

Este tópico utiliza nomes abreviados para repositórios e diretórios. Estes nomes tornam mais fácil de seguir as operações entre os repositórios e diretórios. Este notation (**R** para repositórios de Git e **D** para diretórios locais no seu DSVM) é utilizado nas seguintes secções:

- **R1**: O **GroupProjectTemplate** repositório de Git que o Gestor do grupo configurado no seu servidor de grupo VSTS.
- **R3**: A equipa **ProjectTemplate** repositório de Git que configurou.
- **R4**: O **TeamUtilities** repositório de Git que configurou.
- **D1**: O diretório local clonado a partir do R1 e copiados para D3.
- **D3**: O diretório local clonado a partir do R3, personalizar e copiados para R3.
- **D4**: O diretório local clonado a partir do R4, personalizar e copiados para R4.

Os nomes especificados para os repositórios e diretórios neste tutorial foram fornecidos no pressuposto de que o objetivo consiste em estabelecer um projeto de equipa separada para o seus próprios equipa dentro de um grupo de ciência de dados maior. Mas existem outras opções aberta como antecedência de equipa:

- O grupo inteiro pode optar por criar um projeto de equipa única. Em seguida, todos os projetos de todas as equipas de ciência de dados seria sob este projeto de equipa única. Para tal, pode designar um administrador de git para siga estas instruções para criar um projeto de equipa única. Este cenário poderá ser válido, por exemplo, para:
    -  um grupo de ciência de dados de pequena que não tenha várias equipas de ciência de dados 
    -  um maior dados ciência grupo com várias equipas de ciência de dados que pretenda contudo otimizar a colaboração em equipa entre com atividades como planear sprint de nível de grupo. 
- As equipas podem optar por ter modelos de projeto de equipa específicos ou utilitários de equipa específicos sob o projeto de equipa único para o grupo de todo. Neste caso, os clientes de equipa potenciais clientes potenciais devem criar repositórios de modelo de projeto de equipa e/ou repositórios de utilitários de equipa sob o mesmo projeto de equipa. Nome estes repositórios *< TeamName\>ProjectTemplate* e *< TeamName\>utilitários*, por exemplo, *TeamJohnProjectTemplate*e *TeamJohnUtilities*. 

Em qualquer caso, clientes potenciais clientes potenciais de equipa tem de permitir que os membros da equipa saber quais repositórios de modelo e utilitários adotar quando estão a configurar e repositórios do projeto e utilitários de clonagem. Clientes de potenciais clientes potenciais projeto devem seguir o [tarefas projeto levar para uma equipa de ciência de dados](project-lead-tasks.md) criar repositórios de projeto, se em projetos de equipa separada ou com um projeto de equipa única. 


## <a name="0-prerequisites"></a>0. Pré-requisitos

Os pré-requisitos são satisfeitos, efetuando as tarefas atribuídas para o Gestor de grupo descrito na [Gestor do grupo de tarefas para uma equipa de ciência de dados](group-manager-tasks.md). Para resumir aqui, os seguintes requisitos têm de cumprir antes de começar com as tarefas de oportunidades potenciais equipa: 

- O **servidor do grupo VSTS** (ou conta de grupo em algum outro código de plataforma de alojamento) tiver sido configurada pelo seu Gestor de grupo.
- O **GroupProjectTemplate repositório** (R1) tiver sido configurada na sua conta de grupo, o Gestor de grupo no código de plataforma que planeia utilizar a alojar.
- Ter sido **autorizado** na sua conta de grupo para criar repositórios para a sua equipa.
- Git tem de estar instalado no seu computador. Se estiver a utilizar uma Máquina Virtual de ciência de dados (DSVM), Git tiver sido previamente instalado e estiver pronto para continuar. Caso contrário, consulte o [apêndice plataformas e ferramentas](platforms-and-tools.md#appendix).  
- Se estiver a utilizar um **Windows DSVM**, tem de ter [Gestor de credenciais de Git (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado no seu computador. No ficheiro README.md, desloque para baixo até o **transferir e instalar** secção e clique em de *instalador mais recente*. Isto leva-o para a página de instalador mais recente. Transferir o instalador .exe aqui e executá-la. 
- Se estiver a utilizar **Linux DSVM**, crie uma chave pública SSH no seu DSVM e adicioná-la ao seu servidor VSTS de grupo. Para obter mais informações sobre o SSH, consulte o **criar chave pública SSH** secção o [apêndice plataformas e ferramentas](platforms-and-tools.md#appendix). 
    
## <a name="1-create-a-team-project-and-repositories"></a>1. Criar um projeto de equipa e repositórios

Conclua este passo se estiver a utilizar VSTS como o código de plataforma para controlo de versões e colaboração de alojamento. Esta secção tem de criar três artefactos no servidor de VSTS do seu grupo:

- **MyTeam** projeto no VSTS
- **MyProjectTemplate** repositório (**R3**) no Git
- **MyTeamUtilities** repositório (**R4**) no Git

### <a name="create-the-myteam-project"></a>Criar o projeto de MyTeam

- Aceda a home page de servidor do seu grupo VSTS no URL `https://<VSTS Server Name\>.visualstudio.com`. 
- Clique em **novo** para criar um projeto de equipa. 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- Uma janela de projeto de equipa criar pede-lhe para introduzir o nome do projeto (**MyTeam** neste exemplo). Certifique-se de que seleciona **Agile** como o **modelo de processo** e **Git** como o **controlo de versão**. 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- Clique em **criar projeto**. O projeto de equipa **MyTeam** é criado na menos de 1 minuto. 

- Depois do projeto de equipa **MyTeam** é criado, clique em **para navegar até ao projeto** botão, para ser direcionado para a home page do seu projeto de equipa. 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- Se vir um **Parabéns!** janela de pop-up, clique em de **adicionar código** (botão na caixa vermelha). Caso contrário, clique em **código** (na caixa amarela). Isto direciona-o para a página do repositório de Git do seu projeto de equipa. 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>Criar repositório MyProjectTemplate (R3) no Git

- Na página de repositório de Git do seu projeto de equipa, clique na seta ao lado do nome do repositório descendente **MyTeam**e selecione **gerir repositórios...** .

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- No **controlo de versão** separador do painel de controlo do seu projeto de equipa, clique em **MyTeam**, em seguida, selecione **repositório de mudança de nome...** . 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- Introduza um nome novo para o repositório no **mudar o nome do repositório MyTeam** janela. Neste exemplo, *MyTeamProjectTemplate*. Pode escolher um nome semelhante *< nome da sua equipa\>ProjectTemplate*. Clique em **mudar o nome** para continuar.

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>Criar repositório MyTeamUtilities (R4) no Git

- Para criar um novo repositório de *< nome da sua equipa\>utilitários* no seu projeto de equipa, clique em **novo repositório de...**  no **controlo de versão** separador do seu projeto de equipa painel de controlo.  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- No **criar um novo repositório de** janela que aparece, forneça um nome para este repositório. Neste exemplo, Atribuímos nomes aos como *MyTeamUtilities*, que é **R4** na nossa notação. Escolha um nome semelhante *< nome da sua equipa\>utilitários*. Certifique-se de que seleciona **Git** para **tipo**. Em seguida, clique em **criar** para continuar.

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- Confirme que vê dois novos Git repositórios do criados no seu projeto de equipa **MyTeam**. Neste exemplo: 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4).

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-team-projecttemplate-and-teamutilities-repositories"></a>2. Repositórios de ProjectTemplate e TeamUtilities sua equipa de seed

O procedimento de propagação utiliza os diretórios no seu local DSVM como sites de transição intermédios. Se precisar de personalizar o **ProjectTemplate** e **TeamUtilities** repositórios para satisfazer algumas específico necessidades de equipa, fazê-lo no passo penultimate do procedimento a seguir. Eis um resumo dos passos utilizados para efetuar o seeding o conteúdo a **MyTeamProjectTemplate** e **MyTeamUtilities** repositórios de uma equipa de ciência de dados. Os passos individuais correspondem para que as subsecções no procedimento propagação:

- Repositório de grupo do clone para o diretório local: equipa R1 - clonado para -> local D1
- Clonar os repositórios de equipa em diretórios locais: equipa R3 & R4 - clonado para -> local D3 & D4
- Copie o conteúdo de modelo de projeto de grupo para a pasta de equipa local: D1 - conteúdos copiados para -> D3
- (Opcional) personalização do local D3 & D4
- Enviar conteúdo de diretório local para os repositórios de equipa: D3 & D4 - conteúdo Adicionar -> equipa R3 & R4


### <a name="initialize-the-team-repositories"></a>Inicializar os repositórios de equipa

Neste passo, inicializar o seu repositório de modelo de projeto de equipa do repositório de modelo de projeto de grupo:

- **MyTeamProjectTemplate** repositório (**R3**) da sua **GroupProjectTemplate** (**R1**) repositório


### <a name="clone-group-repositories-into-local-directories"></a>Repositórios de grupo do clone para diretórios locais

Para iniciar este procedimento:

- Crie diretórios no seu computador local:
    - Para **Windows**: **C:\GitRepos\GroupCommon** e **C:\GitRepos\MyTeam**
    - Para **Linux**: **GitRepos\GroupCommon** e **GitRepos\MyTeam** no seu diretório raiz 
- Mude para diretório **GitRepos\GroupCommon**.
- Execute o comando seguinte, conforme adequado, no sistema operativo da sua máquina local.

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

Estes comandos clonagem o **GroupProjectTemplate** repositório (R1) no servidor VSTS de grupo para o diretório local no **GitRepos\GroupCommon** no seu computador local. Após a clonagem, diretório **GroupProjectTemplate** (D1) é criado no diretório **GitRepos\GroupCommon**. Aqui, partimos do princípio que o Gestor do grupo criado um projeto de equipa **GroupCommon**e o **GroupProjectTemplate** repositório está sob este projeto de equipa. 


### <a name="clone-your-team-repositories-into-local-directories"></a>Clonar os repositórios de equipa em diretórios locais

Estes comandos clonagem o **MyTeamProjectTemplate** (R3) e **MyTeamUtilities** repositórios (R4) no seu projeto de equipa **MyTeam** no seu servidor VSTS de grupo para o  **MyTeamProjectTemplate** (D3) e **MyTeamUtilities** (D4) diretórios no **GitRepos\MyTeam** no seu computador local. 

- Mude para diretório **GitRepos\MyTeam**
- Execute os seguintes comandos, conforme adequado, no sistema operativo da sua máquina local. 

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

Após a clonagem, dois diretórios **MyTeamProjectTemplate** (D3) e **MyTeamUtilities** (D4) são criados no diretório **GitRepos\MyTeam**. Podemos ter assumido aqui se denominado o projeto de equipa repositórios de modelo e utilitários **MyTeamProjectTemplate** e **MyTeamUtilities**. 

### <a name="copy-the-group-project-template-content-to-the-local-team-project-template-directory"></a>Copiar o conteúdo de modelo de projeto de grupo para o diretório de modelo de projeto de equipa local

Para copiar o conteúdo do local **GroupProjectTemplate** (D1) pasta local **MyTeamProjectTemplate** (D3), execute um dos seguintes scripts de shell: 

####<a name="from-the-powershell-command-line-for-windows"></a>A partir do PowerShell da linha de comandos para Windows        

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

####<a name="from-the-linux-shell-for-the-linux-dsvm"></a>A partir da shell do Linux para o **DSVM do Linux**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

Os scripts excluir o conteúdo do diretório .git. Os scripts que lhe pedem para fornecer o **concluir caminhos** para o diretório de origem D1 e para o diretório de destino D3.
        

### <a name="customize-your-team-project-template-or-team-utilities-optional"></a>Personalizar o modelo de projeto de equipa ou utilitários de equipa (opcionais)

Personalizar o **MyTeamProjectTemplate** (D3) e **MyTeamUtilities** (D4), se necessário, nesta fase do processo de configuração. 

- Se pretender personalizar os conteúdos do D3 para satisfazer as necessidades específicas da sua equipa, pode modificar os documentos de modelo ou alterar a estrutura de diretórios.

- Se a sua equipa desenvolveu algumas utilitários de que pretende partilhar com a sua equipa de toda, copie e cole estes utilitários diretório D4. 


### <a name="push-local-directory-content-to-team-repositories"></a>Enviar conteúdo de diretório local para os repositórios de equipa

Para adicionar o conteúdo nos diretórios locais (opcionalmente personalizados) D3 e D4 repositórios do equipa R3 e R4, execute o seguinte git bash comandos a partir de uma consola do Windows PowerShell ou a partir da shell do Linux. Execute os comandos do **GitRepos\MyTeam\MyTeamProjectTemplate** diretório.

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

Os ficheiros no repositório de MyTeamProjectTemplate do servidor do seu grupo VSTS são sincronizados quase instantaneamente quando este script é executado.

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

Agora, execute o mesmo conjunto de comandos de git quatro do **GitRepos\MyTeam\MyTeamUtilities** diretório. 

> [AZURE.NOTE]Se esta for a primeira vez, aceita um repositório de Git, terá de configurar parâmetros globais *user.name* e *user.email* antes de executar o `git commit` comando. Execute os seguintes dois comandos:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Se são consolidar para vários repositórios de Git, utilize o mesmo nome e endereço de correio eletrónico ao consolidar a cada um deles. Utilizar o mesmo nome e endereço de correio eletrónico comprova conveniente mais tarde quando criar dashboards do Power BI para controlar as atividades de Git em repositórios de vários.

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3. Criar o agrupamento de dados e análise de recursos (opcional)

Partilha de recursos de dados e análise com a sua equipa toda tem benefícios de desempenho e custo: os membros da equipa podem executar os seus projetos nos recursos partilhados, guarde no orçamentos e colaborar de forma mais eficiente. Nesta secção, iremos fornecer instruções sobre como criar o file storage do Azure. Na secção seguinte, fornecemos instruções sobre como armazenamento de ficheiros do Azure de montagem no seu computador local. Para obter informações adicionais sobre a partilha de outros recursos, tais como dados de ciência de Virtual Machines do Azure, Azure os Clusters do Spark HDInsight, consulte [plataformas e ferramentas](platforms-and-tools.md). Este tópico fornece orientações de uma perspetiva de ciência de dados na seleção de recursos que são adequados para as suas necessidades e hiperligações para páginas de produto e outros tutoriais útil e relevantes que iremos tiver publicado.

>[AZURE.NOTE] Para evitar dados transmitir centros de dados cruzada, que podem ser lenta e dispendiosos, certifique-se de que o grupo de recursos, a conta de armazenamento e a VM do Azure (por exemplo, DSVM) estão no mesmo centro de dados do Azure. 

Execute os scripts seguintes para criar o file storage do Azure para a sua equipa. File storage do Azure para a sua equipa pode ser utilizado para armazenar recursos de dados que são úteis para a sua equipa de toda. Os scripts que lhe pedem para sua informação de conta e subscrição do Azure, por isso, ter estas credenciais prontos introduzir. 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Criar o file storage do Azure com o PowerShell a partir do Windows

Execute este script a partir do PowerShell da linha de comandos:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

Inicie sessão sua conta do Microsoft Azure quando lhe for pedido:

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

Selecione a subscrição do Azure que pretende utilizar:

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

Selecione a conta de armazenamento a utilizar ou criar um novo na subscrição selecionada:

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

Introduza o nome do file storage do Azure para criar. Apenas inferior as maiúsculas e minúsculas carateres, números e - são aceites:

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

Para facilitar a montar e partilhar este armazenamento depois de criado, guarde as informações do armazenamento de ficheiros do Azure para um ficheiro de texto e tome nota do caminho para a respetiva localização. Em particular, é necessário este ficheiro para montar o file storage do Azure para as máquinas virtuais do Azure na secção seguinte. 

É recomendável verificar neste ficheiro de texto para o repositório de ProjectTemplate equipa. Recomendamos que coloque no diretório **Docs\DataDictionaries**. Por conseguinte, este recurso de dados possa ser acedido por todos os projetos na sua equipa. 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Criar o file storage do Azure com um script do Linux

Execute este script de shell do Linux:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

Inicie sessão sua conta do Microsoft Azure seguir as instruções neste ecrã:

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

Selecione a subscrição do Azure que pretende utilizar:

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

Selecione a conta de armazenamento a utilizar ou criar um novo na subscrição selecionada:

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

Introduza o nome do file storage do Azure para criar, carateres de apenas minúsculas, números e - são aceites:

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

Para facilitar a aceder a este armazenamento depois de criado, guarde as informações do armazenamento de ficheiros do Azure para um ficheiro de texto e tome nota do caminho para a respetiva localização. Em particular, é necessário este ficheiro para montar o file storage do Azure para as máquinas virtuais do Azure na secção seguinte.

É recomendável verificar neste ficheiro de texto para o repositório de ProjectTemplate equipa. Recomendamos que coloque no diretório **Docs\DataDictionaries**. Por conseguinte, este recurso de dados possa ser acedido por todos os projetos na sua equipa. 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4. Armazenamento de ficheiros do Azure de montagem (opcional)

Depois de file storage do Azure é criado com êxito, podem ser montados no seu computador local utilizando um dos seguintes scripts do PowerShell ou Linux.

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Armazenamento de ficheiros do Azure de montagem com o PowerShell a partir do Windows

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
É-lhe pedido para iniciar sessão em primeiro lugar, se não iniciou sessão. 

Clique em **Enter** ou **y** para continuar, quando lhe for perguntado se tiver uma informações do armazenamento de ficheiros do Azure de ficheiros e, em seguida, introduza o ***concluir caminho e nome** do ficheiro que criar no passo anterior. As informações para montar um file storage do Azure é ler diretamente a partir de que o ficheiro e está pronto para ir para o passo seguinte.

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Se não tiver um ficheiro que contém as informações de armazenamento de ficheiros do Azure, os passos para a introduzir as informações do teclado são fornecidos no final desta secção.

Em seguida, é-lhe pedido para introduzir o nome da unidade para ser adicionado à máquina virtual. Uma lista de nomes de unidade existente está impresso no ecrã. Deve fornecer um nome de unidade que já não existe na lista.

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

Certifique-se de que uma nova unidade F foi montada com êxito à máquina.

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Como introduzir manualmente as informações do armazenamento de ficheiros do Azure:** se não tiver as informações de armazenamento de ficheiros do Azure num ficheiro de texto, pode seguir as instruções no ecrã seguinte para o tipo na subscrição obrigatório, conta de armazenamento e do Azure informações do armazenamento de ficheiros:

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Escreva o nome de subscrição do Azure, selecione da conta de armazenamento onde o file storage do Azure é criado, e escreva o nome de armazenamento de ficheiros do Azure:

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>Armazenamento de ficheiros do Azure de montagem com um script do Linux

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

É-lhe pedido para iniciar sessão em primeiro lugar, se não iniciou sessão. 

Clique em **Enter** ou **y** para continuar, quando lhe for perguntado se tiver uma informações do armazenamento de ficheiros do Azure de ficheiros e, em seguida, introduza o ***concluir caminho e nome** do ficheiro que criar no passo anterior. As informações para montar um file storage do Azure é ler diretamente a partir de que o ficheiro e está pronto para ir para o passo seguinte.

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

Em seguida, é-lhe pedido para introduzir o nome da unidade para ser adicionado à máquina virtual. Uma lista de nomes de unidade existente está impresso no ecrã. Deve fornecer um nome de unidade que já não existe na lista.

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

Certifique-se de que uma nova unidade F foi montada com êxito à máquina.

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Como introduzir manualmente as informações do armazenamento de ficheiros do Azure:** se não tiver as informações de armazenamento de ficheiros do Azure num ficheiro de texto, pode seguir as instruções no ecrã seguinte para o tipo na subscrição obrigatório, conta de armazenamento e do Azure informações do armazenamento de ficheiros:

- Entrada  **n** .
- Selecione o índice com o nome de subscrição onde o file storage do Azure foi criado no passo anterior:

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- Selecione a conta de armazenamento na sua subscrição e tipo no nome do armazenamento de ficheiros do Azure:

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- Introduza o nome da unidade a ser adicionado ao seu computador, que deve ser diferente de qualquer existentes:

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5. Configurar a política de controlo de segurança 

Na home page do grupo VSTS do seu servidor, clique em de **engrenagem ícone** junto ao seu nome de utilizador no canto superior direito, em seguida, selecione o **segurança** separador. Pode adicionar membros a sua equipa com várias permissões.

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para as descrições das funções e tarefas definidas pelo processo de ciência de dados de equipa mais detalhadas:

- [Grupo do Gestor de tarefas para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de fabrico da equipa de uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas de fabrico de projeto para uma equipa de ciência de dados](project-lead-tasks.md)
- [Contribuintes individuais de projeto para uma equipa de ciência de dados](project-ic-tasks.md)