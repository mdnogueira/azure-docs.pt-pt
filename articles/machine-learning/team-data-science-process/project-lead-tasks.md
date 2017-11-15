---
title: "Equipa dados ciência processo projeto levar tarefas - Azure | Microsoft Docs"
description: "Uma descrição das tarefas para um projeto levar num projeto de equipa de ciência de dados."
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
ms.openlocfilehash: fe7cf1e6167de7d0dfaa5ed75c0b7f5ec23b18cf
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="project-lead-tasks"></a>Tarefas de oportunidades potenciais do projeto

Este tutorial destaca as tarefas que é uma antecedência de projeto esperado concluir a sua equipa de projeto. O objetivo consiste em estabelecer o ambiente de colaboração de equipa que uniformiza no [o processo de ciência de dados de equipa](overview.md) (TDSP). O TDSP é uma arquitetura desenvolvida pela Microsoft que fornece uma sequência de atividades para executar as soluções de Análise Preditiva baseado na nuvem, de forma eficiente structured. Para uma descrição das funções de pessoal e das respetivas tarefas associadas são processados por uma equipa de ciência de dados uniformizar neste processo, consulte o artigo [funções do processo de ciência de dados de equipa e tarefas](roles-tasks.md).

A **projeto levar** gere as atividades diárias de cientistas de dados individual num projeto de ciência de dados específicos. O fluxo de trabalho para as tarefas a ser concluídos por leva de projeto a configurar este ambiente estão representados na figura seguinte:

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Este tópico inclui atualmente tarefas 1,2 e 6 deste fluxo de trabalho de clientes potenciais clientes potenciais do projeto.

>[AZURE.NOTE] Iremos descrevem os passos necessários para configurar um ambiente de equipa TDSP para um projeto com os serviços de equipa do Visual Studio (VSTS) nas instruções seguintes. Especificamos como realizar estas tarefas com VSTS, uma vez que é a forma como implementamos TDSP na Microsoft. Se for utilizada noutra plataforma de alojamento de código para o grupo, as tarefas que necessitam de ser concluídos pela antecedência de equipa, geralmente, não alteram. Mas a forma para concluir estas tarefas vai ser diferentes.


## <a name="repositories-and-directories"></a>Repositórios e diretórios

Este tutorial utiliza nomes abreviados para repositórios e diretórios. Estes nomes tornam mais fácil de seguir as operações entre os repositórios e diretórios. Este notation (R para repositórios de Git) e D para diretórios locais no seu DSVM é utilizado nas seguintes secções:

- **R3**: A equipa **ProjectTemplate** repositório de Git antecedência sua equipa tiver configurado.
- **R5**: O repositório de projeto no Git, o programa de configuração para o seu projeto.
- **D3**: O diretório local clonado a partir do R3.
- **D5**: O diretório local clonado a partir do R5.


## <a name="0-prerequisites"></a>0. Pré-requisitos

Os pré-requisitos são satisfeitos, efetuando as tarefas atribuídas para o Gestor de grupo descrito na [Gestor do grupo de tarefas para uma equipa de ciência de dados](group-manager-tasks.md) e para a equipa de oportunidades potenciais descritos na [tarefas de fabrico da equipa de uma equipa de ciência de dados](team-lead-tasks.md). 

Para resumir aqui, os seguintes requisitos têm de cumprir antes de começar com as tarefas de oportunidades potenciais equipa: 

- O **servidor do grupo VSTS** (ou conta de grupo algumas plataforma de alojamento de código) tiver sido configurada pelo seu Gestor de grupo.
- O **TeamProjectTemplate repositório** (R3) tiver sido configurada na sua conta de grupo pelo seu antecedência de equipa na plataforma de alojamento de código que pretende utilizar.
- Ter sido **autorizado** pelo seu antecedência de agrupamento para criar repositórios na sua conta de grupo para a sua equipa.
- Git tem de estar instalado no seu computador. Se estiver a utilizar uma Máquina Virtual de ciência de dados (DSVM), Git tiver sido previamente instalado e estiver pronto para continuar. Caso contrário, consulte o [apêndice plataformas e ferramentas](platforms-and-tools.md#appendix).  
- Se estiver a utilizar um **Windows DSVM**, tem de ter [Gestor de credenciais de Git (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado no seu computador. No ficheiro README.md, desloque para baixo até o **transferir e instalar** secção e clique em de *instalador mais recente*. Isto leva-o para a página de instalador mais recente. Transferir o instalador .exe aqui e executá-la. 
- Se estiver a utilizar **Linux DSVM**, crie uma chave pública SSH no seu DSVM e adicioná-la ao seu servidor VSTS de grupo. Para obter mais informações sobre o SSH, consulte o **criar chave pública SSH** secção o [apêndice plataformas e ferramentas](platforms-and-tools.md#appendix). 


## <a name="1-create-a-project-repository-r5"></a>1. Criar um repositório de projeto (R5)

- Inicie sessão no seu servidor VSTS grupo *https://\<nome do servidor VSTS\>. visualstudio.com*. 
- Em **projetos recentes & equipas**, clique em **procurar**. Uma janela que aparece apresenta uma lista de todos os projetos de equipa no servidor de VSTS. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Clique no nome do projeto de equipa no qual vai criar o seu repositório de projeto. Neste exemplo, clique em **MyTeam**. 
- Em seguida, clique em **navegar** para ser direcionado para a home page do projeto de equipa **MyTeam**:

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Clique em **Collaborate no código** para ser direcionado para a home page do git do seu projeto de equipa.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Clique na seta descendente no canto superior esquerdo e selecione **+ novo repositório de**. 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- No **criar um novo repositório de** janela, introduza um nome para o repositório de git do projeto. Certifique-se de que seleciona **Git** como o tipo do repositório. Neste exemplo, utilizamos o nome *DSProject1*. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Para criar o ***DSProject1*** repositório de git do projeto, clique em **criar**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. O repositório de projeto DSProject1 de seed

A tarefa aqui é seed o **DSProject1** repositório do projeto (R5) do seu repositório de modelo de projeto de equipa (R3). O procedimento de propagação utiliza os diretórios D3 e D5 no seu local DSVM como sites de transição intermédios. Em resumo, é o caminho de propagação: R3 -> D3 -> D5 -> R5.

Se precisar de personalizar o **DSProject1** repositório de projeto para satisfazer algumas específico necessita do projeto, fazê-lo no passo penultimate do procedimento a seguir. Eis um resumo dos passos utilizados para efetuar o seeding o conteúdo a **DSProject1** repositório de projeto. Os passos individuais correspondem para que as subsecções no procedimento propagação:

- Repositório do modelo de projeto de equipa do clone para o diretório local: equipa R3 - clonado para -> local D3.
- Repositório de DSProject1 do clone para um diretório local: equipa R5 - clonado para -> local D5.
- Copie o conteúdo do modelo de projeto de equipa clonado para clone local do repositório de DSProject1: D3 - conteúdos copiados para -> D5.
- (Opcional) D5 local de personalização.
- Push local DSProject1 conteúdo repositórios de equipa: D5 - conteúdo Adicionar -> equipa R5.


### <a name="clone-your-team-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Clone o repositório de modelo de projeto (R3) de equipa para um diretório (D3) no seu computador local.

No seu computador local, crie um diretório:

- *C:\GitRepos\MyTeamCommon* para Windows 
- *$home/GitRepos/MyTeamCommon* para Linux

Alterar a esse diretório. Em seguida, execute o seguinte comando para clonar o repositório de modelo de projeto de equipa no seu computador local. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Se estiver a utilizar VSTS como a plataforma de alojamento de código, normalmente, o *URL HTTPS do repositório de modelo de projeto de equipa* é:

 ***https://\<nome do servidor VSTS\>.visualstudio.com/\<no nome do projeto de equipa\>/_git/\<o nome de repositório de modelo de projeto de equipa\>***. 

Neste exemplo, temos de:

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Se estiver a utilizar VSTS como a plataforma de alojamento de código, normalmente, o *SSH URL do repositório de modelo de projeto de equipa* é:

***SSH: / /\<nome do servidor VSTS\>@\<nome do servidor VSTS\>.visualstudio.com:22/\<o nome do projeto de equipa > /_git/\<seu repositório de modelo de projeto de equipa nome\>.*** 

Neste exemplo, temos de:

***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>Clone o repositório de DSProject1 (R5) para um diretório (D5) no seu computador local

Diretório de alteração para **GitRepos**, e execute o seguinte comando para clonar o repositório de projeto para o computador local. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Se estiver a utilizar VSTS como a plataforma de alojamento de código, normalmente, o _URL HTTPS do repositório projeto_ é ***https://\<nome do servidor VSTS\>.visualstudio.com/\<sua equipa Nome do projeto > /_git/ < no nome do repositório de projeto\>***. Neste exemplo, temos ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Se estiver a utilizar VSTS como a plataforma de alojamento de código, normalmente, o _SSH URL do repositório projeto_ é _ssh: / / < nome do servidor VSTS\>@< nome do servidor VSTS\>.visualstudio.com:22/<Your Team Project Name> / \_git / < nome do repositório do projeto\>. Neste exemplo, temos  ***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1*** .

### <a name="copy-contents-of-d3-to-d5"></a>Copiar conteúdos de D3 para D5 

Agora no seu computador local, tem de copiar o conteúdo de _D3_ para _D5_, exceto os metadados do git no diretório .git. Os scripts seguintes executará a tarefa. Certifique-se ao tipo nos caminhos corretos e completos para os diretórios. Pasta de origem é o para a sua equipa (_D3_); a pasta de destino é o para o seu projeto (_D5_).    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

Agora pode ver na _DSProject1_ pasta, todos os ficheiros (excluindo o .git) são copiados do _MyTeamProjectTemplate_.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

Agora pode ver na _DSProject1_ pasta, todos os ficheiros (exceto os metadados no .git) são copiados do _MyTeamProjectTemplate_.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>Personalizar D5 se for necessário (opcional)

Se o seu projeto tem algumas diretórios específicos ou documentos, sem ser aqueles que aproveitar o modelo de projeto de equipa (copiado para o seu diretório D5 no passo anterior), pode personalizar o conteúdo de D5 agora. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-vsts-server"></a>Adicionar conteúdo de DSProject1 D5 para R5 no seu servidor VSTS de grupo

Tem agora push o conteúdo  **_DSProject1_**  para _R5_ repositório no seu projeto de equipa no servidor VSTS do seu grupo. 


- Mude para diretório **D5**. 
- Utilize os seguintes comandos de git para adicionar o conteúdo no **D5** para **R5**. Os comandos são os mesmos para sistemas Windows e Linux. 
    
    Adicione o git do Git estado.
    push de git do Git consolidação -m "push de win DSVM"
    
- Consolide as alterações e push. 

>[AZURE.NOTE] Se esta for a primeira vez, aceita um repositório de Git, terá de configurar parâmetros globais *user.name* e *user.email* antes de executar o `git commit` comando. Execute os seguintes dois comandos:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Se são consolidar para vários repositórios de Git, utilize o mesmo nome e endereço de e-mail em todos eles. Utilizar o mesmo nome e endereço de correio eletrónico comprova conveniente mais tarde quando criar dashboards do Power BI para controlar as atividades de Git em repositórios de vários.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Criar e montar o file storage do Azure como recursos de projeto (opcional)

Se pretender criar o file storage do Azure para partilhar dados, tais como o projeto de dados não processados ou as funcionalidades geradas para o seu projeto, para que todos os membros de projeto tenham acesso aos mesmos conjuntos de dados de vários DSVMs, siga as instruções nas secções 3 e 4 de [ Tarefas de antecedência para uma equipa de ciência de dados de equipa](team-lead-tasks.md). 


## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para as descrições das funções e tarefas definidas pelo processo de ciência de dados de equipa mais detalhadas:

- [Grupo do Gestor de tarefas para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de fabrico da equipa de uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas de fabrico de projeto para uma equipa de ciência de dados](project-lead-tasks.md)
- [Contribuintes individuais de projeto para uma equipa de ciência de dados](project-ic-tasks.md)